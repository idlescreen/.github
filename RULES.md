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

| Tier | What it means | Benched? | Gated in CI? | Typical page |
|---|---|---|---|---|
| **T1** | Hot path: runs per frame, per save, per display update. A regression here is a user-visible regression. | yes | **yes — fails the build** | `upscale_stretch_into`, `apply_fade_in`, every saver's `Screensaver` |
| **T2** | Allocation-, lock- or syscall-sensitive, but not per-frame. A regression is a resource regression. | yes, on demand | no | `stretch_cache`, `frame_pool`, `overlay::epoll`, `power_watcher` |
| **T3** | Correctness only. | no | no | config parsers, CLI arg handling, formatters |

The tier is a claim about *consequence*, not about importance. A T3
page is not a lesser page; it is a page whose slowness nobody would
ever notice.

### The label

A page states its tier on line 2 (after the SPDX header), in a
single `// perf:` comment:

```rust
// perf: T1 · bench: stretch · gate: perf-baseline.json
// perf: T1 · bench: tick · sym: Screensaver · gate: perf-baseline.json
// perf: T2 · bench: hot_path · on-demand only; not gated
// perf: T2 · bench: none · ubuntu-latest is x86_64; promotion to T1 needs an aarch64 runner
```

Fields, separated by `·` (U+00B7):

- `perf:` — `T1`, `T2`, or `T3`. Required.
- `bench:` — the `[[bench]]` target that exercises this page, or
  `none` with the reason. Required for T1, optional for T2, banned
  for T3.
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

`scripts/check-perf-labels.sh` runs in CI in every repo that has
labels. It polices T1 *claims*, not T1 *coverage*:

- a T1 `bench:` must name a real target, and that target's source
  must actually reference the page's symbol;
- a T1 `gate:` must name a baseline that exists;
- a T2 `bench:` must still exist, so the claim cannot rot;
- a T3 page may not claim a bench at all;
- a page with no label is simply not gated. Silence is allowed.
  A false claim is not.

### The baseline pipeline

`scripts/refresh-baseline.sh` runs the T1 targets and reads
criterion's own machine output — `target/criterion/**/new/estimates.json`
— never scraped stdout. The old version parsed the human-readable
`time: [a b c]` line, whose format has shifted between criterion
releases; a regex that stopped matching produced a comparison with
zero entries, which the old pipeline reported as a pass.

`scripts/compare-bench.py` reads the same JSON and reports each
bench's `median_ns` and `median_abs_dev_ns`. Zero parseable
entries is a hard error, not a green build. A T1 regression past
threshold fails the build; the PR comment is posted *before* the
gate so the author sees why it went red.

Baselines live at `runtime/perf-baseline.json` and
`savers/perf-baseline.json`, both regenerated by the refresh script
in their own repo.

**A caveat we state out loud:** these baselines are captured on
whatever machine ran the refresh script, and CI compares them on a
shared GitHub-hosted runner. A 5% threshold on a shared runner is a
canary, not a measurement. Tightening it honestly needs a
self-hosted, pinned-hardware runner; until then, treat a red T1 as
"go look," not as "this change is 6% slower."

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

| Repo | 16-256 cap gate? | One-fn-per-page? | Perf tiers? |
|---|---|---|---|
| `runtime` | ✅ enforced | migrating | ✅ 7 T1 + 7 T2 labels; linter + gate live |
| `savers` | ✅ enforced | partial | ✅ 11 T1 labels; linter + gate live |
| `cli` | ✅ enforced | ✅ already mostly | none yet |
| `studio` | ✅ enforced | mostly | none yet |
| `cosmic` | ✅ enforced | ✅ | n/a |
| `tui` | ✅ enforced | ✅ | n/a |
| `packages` | ✅ enforced | partial | n/a |
| `idlescreen.github.io` | n/a | ✅ | n/a |
| `idlescreen/.github` | n/a | ✅ | n/a |

The "migrating" / "none yet" rows are the ones where the rule
becomes real with new commits. Adding a `// perf:` label to a
repo with no `scripts/check-perf-labels.sh` is inert — the label
is a note to yourself until the linter ships. Per-repo CI gets the
same shell stanza; per-repo reviewers police the function-name /
one-fn-per-page pattern in code review.

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
