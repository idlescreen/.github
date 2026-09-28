# idlescreen rules

> One function per page. The page proves it.

This file is the canonical rule book. Per-repo `AGENTS.md`,
`CONTRIBUTING.md`, and `.github/workflows/ci.yml` files defer to
here. If a repo's CI disagrees with this file, this file wins;
file a follow-up to fix the CI.

---

## 1. Code pages: 16-256 lines per file

**Every source file is a "page".** A page is 16 to 256 lines,
inclusive, counted with `wc -l` on the file's natural source
(comments and blank lines are part of the page; split tests
into a sibling `_tests.rs` file if they push a page over the
cap — we already use this pattern in `frame_loop.rs +
frame_loop_tests.rs`).

The lower bound (16) catches whole-function files that should
be inlined or folded into a sibling. The upper bound (256) is
the entropy ceiling — anything bigger is doing two or more
things and should be split.

The rule is Rust-only. It covers every `.rs` file in `runtime/`,
`savers/`, `studio/`, `cli/`, `tui/`, `cosmic/`, `packages/`, and
`idlescreen.github.io/`. It does not cover `.c`, `.h`, or `.sh`:
a C header is a unit of linkage, not a unit of thought, so a line
count on it measures something other than what this rule is for. CI
scripts stay governed by shellcheck, which is a better fit for them
than a page-size rule borrowed from Rust.

Build artifacts under `target/`, `.dist/`, container overlays,
CI cache directories, and version control metadata are
excluded via `find -not -path …`.

The CI gate is one shell stanza per enforcing repo:

```bash
over=$(find . -name '*.rs' \
    -not -path './target/*' -not -path './dist/*' -not -path './.git/*' \
    -not -path './.agents/*' -not -path './node_modules/*' \
    -not -path './.local/*' -not -path './.containers/*' \
    -not -path './.cache/*' \
    | xargs wc -l \
    | awk '$1 > 256 || $1 < 16')
[ -z "$over" ] || { echo "Pages out of 16-256 range:"; echo "$over"; exit 1; }
```

`benches/*.rs` are pages too. A bench harness is product code that
touches the hot path; a 400-line harness is a second entropy problem,
not a way around the first.

Sub-repos that don't enforce this gate today are listed in
`#8` below; they will once their first `.rs` file is migrated.

---

## 2. One function per page

**The page is named after the function it implements.** That is
the load-bearing rule. Everything else flows from it.

- A `.rs` file's primary subject is a single public function or
  method. Helpers and small types specific to that function live
  in the same file. Cross-cutting types and traits live in a
  sibling module (e.g. `_types.rs` or a trait module).
- File name = function name, lowercased + underscored.
  `pub fn tick_loop_until_shutdown` lives in
  `tick_loop_until_shutdown.rs`. `pub struct FrameSignal
  { … }` with its `wait_for` / `notify` methods lives in
  `frame_signal.rs`.
- When a function clusters many responsibilities such that
  its primary implementation spills past 256 lines:
    - Public entry: `function_name.rs` (≤ 256 lines, exposes
      the public surface).
    - Helpers: `function_name/_helpers.rs` (a directory
      containing helper files, each ≤ 256 lines and named
      after the helper it provides).
    - Test + bench (preferred) or extract to sibling
      `function_name_tests.rs` if tests alone overflow the cap.
- A file that doesn't have one obvious function subject is a
  refactor candidate. If you can't name it after a function, you
  have two functions in the file. Split.

This is Linus's "good taste": pull the special case out of
the code that needs it. Each page does one thing well, has a
name that says what it does, and composes with the others.

---

## 3. Function naming

**Function names describe what the function does, in system
terms, not its type signature.**

- Verb forms for actions: `schedule_one_tick`,
  `format_bgra_pixel`, `read_power_supply`. The verb is the
  real action. Noun forms for pure computations: a public
  struct `BufferPool` has methods that read as verbs on it
  (`pool.acquire()`, `pool.release()`).
- The name should read like the function's spec. A reader who
  knows nothing about the codebase can guess what `fn
  pick_lowest_priority_inhibitor(inhibitors:
  &[Inhibitor]) -> &Inhibitor` does.
- Forbidden names: `handle_*`, `do_*`, `process_*`, `run_*`,
  `make_*` as a verb. These are weasel-words that hide what the
  function actually does. If you can't replace them with a
  specific verb, the function is doing more than one thing and
  should be split.
- No silent abbreviations. `fs::read` is fine because `fs` is
  the Unix-standard term; `fn rst_…` for "restart" is not. If
  the abbreviation isn't load-bearing in C/POSIX/unix, spell
  it out.
- Constructors: `Foo::new()` only when there is exactly one
  sensible construction. When there is more than one, name the
  constructors: `from_cwd`, `from_path`, `from_terminal_cells`,
  `from_shm_pool`. The function name documents the preconditions.
- `unwrap`, `expect`, `panic` callers get their hand bitten.
  Their replacements are named `or_fail` / `or_bail` /
  `must_get`, all of which return a typed error.

---

## 4. QA test per function

**Every public function has a QA test in the same file (or
sibling `_tests.rs` if tests alone overflow the cap).** This is
tier-independent: every function gets tested, and the test is
what earns a page the right to claim a lower tier in §5.

`#[cfg(test)] mod tests { … }` immediately under the function's
file. Tests cover:

- **Name promise**: the assertion proves the function does what
  its name says.
- **Edge cases**: zero, one, many; min, max, NaN/null/None
  where applicable; boundaries.
- **Error paths**: failure modes return errors or panic with
  clear messages (the test asserts behavior, not absence).

Tests are property-style where it makes sense: a single test
function exercises a bunch of related inputs. Avoid tests that
just assert `trivially_true_thing()` — those are cargo cults.

A function with `unsafe` blocks gets a `function_name_safety_*`
test proving the safety invariants.

### A test must terminate

**Every test terminates.** Not "fails quickly" — terminates. A test
that hangs is worse than a failing one, because a failing test names
its cause and a hanging one just eats a runner until someone kills it.

The specific trap: a test whose subject is a *non-blocking consumer*
must not put back-pressure on the producer side. `sync_channel(1)`
plus two `send`s and no receiver running is a deadlock, and libtest
joins every test thread before exit, so the whole suite hangs rather
than reporting the failure. Use an unbounded channel and drop the
sender before draining, so `try_recv` terminates on `Disconnected`
rather than depending on a coincidental `Empty`. Reach for `try_send`
where the point of the test is that the producer never blocks.

This is not hypothetical. It is how a single test in
`wayland-present` hung `cargo test --workspace` past 600 seconds, and
why every CI job in the org now carries an explicit `timeout-minutes`:
the bound should turn the next one into a red build rather than a
burned runner.

Correctness is a floor, not a tier. A page that passes its tests
and nothing else is **T3 — QA test only**: covered, not
measured. Most of the codebase is T3, and that is a legitimate
answer for a config parser or a git-status formatter. What is not
legitimate is calling a T3 page a T1.

---

## 5. Performance: three tiers, not one slogan

**There is no such thing as "a benchmark per function."** The
org has 14 real `[[bench]]` targets and ~962 public functions.
A rule that demands a bench for each of them gets satisfied by 962
benches nobody runs, which is worse than no rule because it looks
like rigor.

So we tier, and we only promise what we gate.

| Tier | What it means | Benched? | In CI? | Typical page |
|---|---|---|---|---|
| **T1** | Hot path: runs per frame, per save, per display update. A regression here is a user-visible regression. | yes | **measured every run; fails the build only on a doubling** — see §6 | `upscale_stretch_into`, `apply_fade_in`, every saver's `Screensaver` |
| **T2** | Allocation-, lock- or syscall-sensitive, but not per-frame. A regression is a resource regression. | yes, on demand | measured, never gates | `stretch_cache`, `frame_pool`, `overlay::epoll`, `power_watcher` |
| **T3** | Correctness only. | no | no | config parsers, CLI arg handling, formatters |

The tier is a claim about *consequence*, not about importance. A T3
page is not a lesser page; it is a page whose slowness nobody would
ever notice.

**T1 gates on a doubling, not on 5%, and that is a hardware fact
rather than a standard we chose.** Two `ubuntu-latest` runs of
byte-identical code, minutes apart, differ by a median of 9.8% with a
p90 of 63.0% and a worst case of +94.5%; 42 of the 73 runtime T1
benches moved more than 5% between them. `ubuntu-latest` is a label,
not a machine. A 5% gate on it false-fails the majority of the suite
on every single run, which teaches everyone to read red as noise.

So the promise is the one the hardware can keep: every T1 bench is
measured on every run, a delta past 5% is reported loudly as
ADVISORY, and only a doubling fails the build. That still catches the
regressions that matter at this stage — a hot path that became
quadratic, a cache that stopped hitting, a SIMD fast path that fell
back to scalar. It will not catch a 20% regression, and no threshold
available here can, because 20% is inside the noise. Tightening this
needs a self-hosted pinned-hardware runner, and that is the real fix
rather than a smaller number.

### The label

**Every page has one.** Not most — every. A page with no label is a
page nobody has thought about, and that is the single failure mode
this rule exists to prevent. The linter fails the build on an
unlabelled page.

A page states its tier and its detection mechanism on line 2 (after
the SPDX header), in a single `// perf:` comment:

```rust
// perf: T1 · bench: stretch · gate: perf-baseline.json · check: bench
// perf: T1 · bench: draw_frame · sym: render_content_viewport_into · gate: perf-baseline.json · check: bench
// perf: T2 · bench: hot_path · on-demand only · check: bench
// perf: T2 · bench: none · metric: aarch64-only, never compiled by the x86_64 runner · check: review
// perf: T3 · metric: touches the filesystem; dominated by syscall latency · check: test
// perf: T3 · metric: process-exit, never on a hot path · check: review
```

Fields, separated by `·` (U+00B7):

- `perf:` — `T1`, `T2`, or `T3`. Required.
- `check:` — **how a machine would notice if this page's cost
  changed**: `bench`, `test`, or `review`. Required on every page.
- `bench:` — the `[[bench]]` target that exercises this page, or
  `none` with the reason. Required for T1/T2, banned for T3.
- `metric:` — what this page costs, in words. Required for T3, and
  for a T2 that cannot be benched.
- `sym:` — the symbol to look for in the bench source. Defaults to
  the filename minus `.rs`. It exists because some pages *cannot*
  match their own name: a `Screensaver` trait impl must stay
  co-located with the trait, so `screensaver_impl.rs` contains no
  callable symbol called `screensaver_impl`. Those pages carry
  `sym: Screensaver`.
- `gate:` — the baseline file the regression is checked against.
  T1 only.

The label names its own evidence. That is the point: you should be
able to check a performance claim by reading one line, without
opening the function.

#### `check:` — the question the tier alone could not answer

"What tier is this page?" is a claim about consequence. "How would we
know if it got slower?" is a claim about *evidence*, and the two are
not the same question. A tier says a page matters; `check:` says
whether anything is actually watching it.

| `check:` | What watches the page | Where |
|---|---|---|
| `bench` | criterion measures it; CI compares the median against a baseline | `perf.yml` |
| `test` | a property test asserts it — call counts, allocation counts, no-syscall invariants | `cargo test` |
| `review` | nothing automated; a human checks the `metric:` claim when the page changes | code review |

`test` is the one that does the most work, and it is the answer to the
page that is too fast to time. `apply_fade_in/past_500ms_noop`
baselines at 0.5ns — a fraction of a clock tick — so a benchmark there
can only ever report a different rounding of zero. A property test
("this no-ops without touching the allocator") is machine-checkable,
carries no noise, and catches the change that actually matters.

`review` is the honest floor, not a pass. It exists because some
pages cannot be checked: `idle_dbus::locks::poison_or_exit` exits the
process, so there is nothing to call in a loop and nothing to assert
on. Labelling that `check: bench` would be a false claim, and a false
claim is worse than an honest gap.

`check:` is validated, not trusted. Claiming `check: test` on a page
with no `#[test]` fails the build, which is the whole point: the field
is an assertion about the page, so CI holds you to it.

### Benches live in `[[bench]]` targets, never inline

`#[cfg(test)] mod benches { … }` is **banned**. Inline blocks don't
compile in `--release` for the shape we need, they can't be filtered
or run individually, and they quietly rot — the org carried 17 of
them that no CI job had ever executed.

A bench is a `[[bench]]` target in the owning crate's `benches/`,
declared with `harness = false` and using `criterion_main!`. A
private page reached from a bench goes through a `#[doc(hidden)] pub
mod bench_exports` seam in the module that owns it; that is a
measurement seam, not public API, and `rustdoc` hides it.

### The gate

`scripts/check-perf-labels.sh` runs in CI in **all eight repos** —
it is pure bash/grep/awk, so it rides along with a checkout the job
already has rather than costing a job of its own. It enforces:

- **every page has a label.** An unlabelled `.rs` file fails the
  build. This is the rule that changed most recently and it is the
  one worth defending: silence used to be allowed, and the cost of
  that silence was a page like `config.rs` reading the disk three
  times where it used to read once, with nothing anywhere to notice.
- every page names a `check:`, and it is one of `bench`, `test`,
  `review`;
- a T1 `bench:` must name a real target, and that target's source
  must actually reference the page's symbol;
- a T1 `gate:` must name a baseline that exists;
- a T1/T2 page claiming `check:` other than `bench` fails — they are
  measured by criterion, so anything else is a false claim;
- a T3 page may not claim a bench, and must state a `metric:`;
- `check: test` requires a `#[test]` on the page. The field is an
  assertion, so CI holds you to it.

Labels are generated from the page's own contents by
`scripts/label-perf-pages.py`, which derives each `metric:` from a
signal a reviewer can confirm by reading the file — a `fs::read`
means "touches the filesystem", a `Command::new` means "spawns a
subprocess". It never invents a claim. Running it is how a new page
gets its line; the linter is what stops the line from being a lie.

### The baseline pipeline

`scripts/refresh-baseline.sh` runs the T1 targets and reads
criterion's own machine output — `target/criterion/**/new/estimates.json`
— never scraped stdout. The old version parsed the human-readable
`time: [a b c]` line, whose format has shifted between criterion
releases; a regex that stopped matching produced a comparison with
zero entries, which the old pipeline reported as a pass.

`scripts/compare-bench.py` reads the same JSON and reports each
bench's `median_ns` and `median_abs_dev_ns`. Zero parseable
entries is a hard error, not a green build. A T1 bench past the 5%
advisory line is reported and counted; only a doubling fails the
build, and the PR comment is posted *before* the gate so the author
sees why it went red.

Baselines live at `runtime/perf-baseline.json` and
`savers/perf-baseline.json`.

**The baseline is captured on the runner that gates it.**
`.github/workflows/perf-baseline.yml` runs the T1 targets on the same
`ubuntu-latest` label `perf.yml` gates on, with the same toolchain and
a byte-identical system-package list, and commits the result straight
to master. Weekly, plus `workflow_dispatch` on demand. It must not
fan out across a matrix: no single runner would then hold a
comparable machine, and every comparison would be noise again. One
runner, one machine, one baseline.

It commits rather than opening a PR because the org does not grant
`GITHUB_TOKEN` permission to create pull requests. That trade is
acceptable only because the baseline is runner-captured; the commit
message states the provenance, and the file records `captured_at` and
`commit`.

Moving the baseline onto the runner was necessary but not sufficient.
It removed a 10.4% median offset between a dev box and a runner, and
the gate *still* went red on the next run, because `ubuntu-latest` is
a scheduling label rather than a machine spec. The same code, run
twice on that label, gave:

    median |delta|    9.8%
    p90     |delta|   63.0%
    worst   |delta|   94.5%
    past 5%           42 of 73

The tell is uniformity, not size. `letterbox/nearest_*` moved +63.0%
to +63.4% across nine different resolutions on one runner. No code
change produces identical percentages at every size; a different host
CPU does. `letterbox/linear_*` on that same runner agreed to within
1%.

**The honest limit:** a shared runner can resolve a doubling and
nothing finer. Treat an ADVISORY as "go look", never as "this change
is 6% slower". Pinning the hardware means a self-hosted runner, and
until one exists, a tighter number in `GATE_PCT` would be a claim the
hardware cannot support.

**A percentage needs a number worth taking a percentage of.**
`apply_fade_in/past_500ms_noop` baselines at 0.5ns — a fraction of a
clock tick — and the first run reported `0.00 -> 0.00 us (+26.7%)`
and failed the build on it. That is a different rounding of zero, not
a slowdown. `compare-bench.py` therefore exempts any bench whose
*current* median is below `MIN_GATED_MEDIAN_NS` (10ns): still
printed, with its real numbers, never able to fail a build on its own.

The floor is 10ns and not 1µs, and that choice is load-bearing. Real
T1 benches are fast — `chaos_update` is 72ns, `ripple_update` 215ns,
the bilinear SIMD paths 38ns — and those are exactly the pages most
worth watching. A 1µs floor would have silently un-gated roughly
fifteen of them. The floor applies to the current median only, so a
bench that was a no-op and now does real work still fails.

---

## 6. Zero-trust: claims carry data

"Performance improved by X." is not a claim we accept. We
accept:

- "PR #N ran `scripts/compare-bench.py perf-baseline.json
  target/criterion` against the baseline from `2b65cc9`;
  `stretch_u32_rows/320x180_to_1920x1080` median 731.90 → 688.24 µs
  (−6.0%, noise ±0.8%); every other T1 bench within threshold."
- "PR #N compared `perf-baseline.json` before and after;
  `median_ns` fell 4.2% while `median_abs_dev_ns` stayed at ~1%,
  so the delta is above this runner's noise."
- "Branch `dead-path-removal` lands comment block +
  `cargo test --release --workspace`; 0 changed, 0 failed."
- "`cargo clippy --workspace --all-targets -- -D warnings`
  clean."

If a claim has no number, it didn't happen. If it has a number
but no methodology, it's a guess. If the number came from
scraping human-readable output, it's a guess with extra steps.

Report the noise alongside the delta. A 6% improvement measured
with a 9% median absolute deviation is not an improvement, it is a
lucky afternoon — and `compare-bench.py` prints `median_abs_dev_ns`
precisely so you can't quietly drop that half of the sentence.

Which is also why the comparator has a third verdict. When a run's
own noise exceeds the threshold being applied, it reports
**INCONCLUSIVE**, not `ok`:

    cosmos_update/update_80x24: 24.76 -> 22.12 us (-10.7%, noise ±53.9%)
      [INCONCLUSIVE (noise ±53.9% > 5.0%)]

A ±54% spread measured against a 5% threshold is a coin flip.
Printing `ok` there would be a claim the run cannot support, and
silently gating on it would be worse. INCONCLUSIVE is neither a pass
nor a failure: it says the bench needs a quieter machine — a pinned
self-hosted runner — before it can gate anything. Fixing that is
per-bench work, and the verdict names which bench is asking for it.

---

## 7. Linux/Unix posture

We work in the unix tradition. The org's code talks to
`/sys/class/power_supply`, `memfd_create(2)`, `eventfd(2)`,
`epoll(7)`, `signalfd(2)`, `SCM_RIGHTS`, `prctl(2)`, and the
libwayland protocol. We don't ship FFI shims around POSIX
that hide what the kernel is doing.

When a feature can be done in 50 lines of `libc` + an ioctl,
we don't pull a 200k-line library crate. When a Rust API
hides what the kernel actually does, we drop down to `libc`.

`unsafe` is a tool, not a sin. Every `unsafe` block has a
safety comment. Every `unsafe fn` has tests that exercise the
unsafe paths. Bare `unsafe` `Box::from_raw` with no
documentation gets reverted.

---

## 8. Per-repo enforcement state

Every `.rs` page in every code repo now carries a `// perf:` label, and
every repo runs `scripts/check-perf-labels.sh`, which fails the build on
an unlabelled page. 622 pages, zero gaps.

| Repo | 16-256 cap gate? | One-fn-per-page? | Pages | T1 | T2 | T3 | bench | test | review |
|---|---|---|---|---|---|---|---|---|---|
| `runtime` | ✅ enforced | migrating | 278 | 8 | 7 | 263 | 14 | 118 | 146 |
| `savers` | ✅ enforced | partial | 184 | 11 | 0 | 173 | 11 | 53 | 120 |
| `cli` | ✅ enforced | ✅ already mostly | 47 | 0 | 0 | 47 | 0 | 13 | 34 |
| `studio` | ✅ enforced | mostly | 56 | 0 | 0 | 56 | 0 | 23 | 33 |
| `cosmic` | ✅ enforced | ✅ | 12 | 0 | 0 | 12 | 0 | 4 | 8 |
| `tui` | ✅ enforced | ✅ | 8 | 0 | 0 | 8 | 0 | 3 | 5 |
| `idlescreen` | ✅ enforced | ✅ | 3 | 0 | 0 | 3 | 0 | 1 | 2 |
| `packages` | ✅ enforced | partial | 34 | 0 | 0 | 34 | 0 | 23 | 11 |
| `idlescreen.github.io` | n/a | ✅ | n/a | n/a | n/a | n/a | n/a | n/a | n/a |
| `idlescreen/.github` | n/a | ✅ | n/a | n/a | n/a | n/a | n/a | n/a | n/a |

The `review` column is the honest floor, not a pass. 358 of those 359
pages are T3 — config, discovery, error mapping, build scripts, bench
harnesses. The one T2 is `idle-upscaler/src/cpu/bilinear_neon.rs`, which
is aarch64-only and is never compiled by the x86_64 CI, so a criterion
target for it would measure nothing. T3 pages inside a benched crate are
usually covered transitively: the T1 bench drives the crate's public
`update`/`draw` entry points, not a leaf, so a regression in
`cosmos/physics/merges.rs` still moves the gated `cosmos/mod.rs` number.
Reachability has not been proven mechanically for all 358, and that is the
gap worth closing next.

The "migrating" / "partial" rows are the ones where the rule becomes real
with new commits. Per-repo CI runs the same shell stanza; per-repo
reviewers police the function-name / one-fn-per-page pattern in review.

### A green release run must mean the release worked

The step that pings `idlescreen/packages` after a release is **advisory,
never fatal**. It used to `exit 1` when
`IDLESCREEN_PACKAGES_DISPATCH_TOKEN` was missing, which marked a release
that published perfectly as a failed run — and the org secret that is
supposed to cover it is not reaching `cli` or `idlescreen`, so this was
firing for real. That is the worst possible failure mode: it trains
readers to ignore red on this workflow.

It is safe to be lenient because the dispatch is a latency optimisation
and not a dependency. `packages/.github/workflows/import-release.yml`
runs a "Sweep latest releases for missing pool files" step on a 6-hourly
cron that converges the pool to every product repo's latest release
whether or not a dispatch ever arrived. A missing token costs at most one
sweep window. Verified: `idlescreen v4.0.4` lost its dispatch exactly
this way, the sweep imported it unaided, and `idlescreen_4.0.4-1_amd64.deb`
reached the live index within minutes of a manual `workflow_dispatch`.

Both failure modes now `::warning::` and exit 0. A missing *secret* is
not a build failure; a missing *artifact* is.

---

## 9. The exception principle

The rule is the rule. Exceptions exist for the same reason
broken windows exist: silence the deviation, file an issue
referencing this document, and either reshape the rule or
land the exception as a permanent carve-out in this file.

If you find yourself "just adding a few more lines" past 256,
that's a smell. If you find yourself writing `handle_stuff`,
that's a smell. Smells are signals, not bugs — listen to them.

---

_Last revised_: see git log of this file.
