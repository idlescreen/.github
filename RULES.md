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

Recursive: the rule applies to every `.rs`, `.c`, `.h`, `.sh`
file in `runtime/`, `savers/`, `studio/`, `cli/`, `tui/`,
`cosmic/`, `packages/`, and `idlescreen.github.io/`. Build
artifacts under `target/`, `.dist/`, container overlays,
CI cache directories, and version control metadata are
excluded via `find -not -path …`.

The CI gate is one shell stanza per enforcing repo:

```bash
over=$(find . \( -name '*.rs' -o -name '*.c' -o -name '*.h' -o -name '*.sh' \) \
    -not -path './target/*' -not -path './dist/*' -not -path './.git/*' \
    -not -path './.agents/*' -not -path './node_modules/*' \
    -not -path './.local/*' -not -path './containers/*' \
    -not -path './.cache/*' \
    -exec wc -l {} + \
    | awk '$1 > 256 || $1 < 16 {print}')
[ -z "$over" ] || { echo "Pages out of 16-256 range:"; echo "$over"; exit 1; }
```

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
sibling `_tests.rs` if tests alone overflow the cap).**

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

Sentinel test pattern (when a function has invariants worth
firing on each CI run):

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn function_name_holds_for_promised_inputs() { … }

    #[test]
    fn function_name_rejects_empty_input() { … }

    #[test]
    fn function_name_does_not_allocate_on_cache_hit() { … }
}
```

A function with `unsafe` blocks gets a `function_name_safety_*`
test proving the safety invariants.

---

## 5. Performance benchmark per function

**Every public function has a criterion benchmark with the same
name as the function.**

`#[cfg(test)] mod benches { … }` block in the same file (or
sibling `function_name_bench.rs` if both tests + bench push the
file over 256). Use criterion 0.5's `criterion_group!` /
`criterion_main!` macros; expose the `c.bench_function("…", |
b| b.iter(|| …))` for the function's main path.

A bench without a name-matching function gets deleted. A
function without a bench gets paired with one. The bench name
must be discoverable by reading the bench block — `bench_<fn>`
with the function's verb form.

Benched functions are the ones we promise measurements on.
The org-wide perf baseline lives at
`runtime/perf-baseline.json` (and per-repo siblings);
`scripts/refresh-baseline.sh --comment <bench-name>` regenerates
it on the next push. Regression thresholds are per-bench in
the same script.

When a function changes for any reason, its bench is the
before/after anchor. A change without a benchmark delta is
suspicious — either the change is wrong or the bench doesn't
actually exercise the function's hot path.

---

## 6. Zero-trust: claims carry data

"Performance improved by X." is not a claim we accept. We
accept:

- "PR #N measured `cargo bench --bench fn_name` against the
  prior baseline; p50 dropped 14.2% (173ns → 148ns); p99
  unchanged within noise."
- "Branch `dead-path-removal` lands comment block +
  `cargo test --release --workspace`; 0 changed, 0 failed."
- "`cargo clippy --workspace --all-targets -- -D warnings`
  clean."

If a claim has no number, it didn't happen. If it has a number
but no methodology, it's a guess.

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

| Repo | 16-256 cap gate? | One-fn-per-page? | Bench per fn? |
|---|---|---|---|
| `runtime` | ✅ enforced | migrating | ✅ criterion benches landed |
| `cli` | ✅ enforced | ✅ already mostly | mostly |
| `studio` | ✅ enforced | mostly | partial |
| `idlescreen/.github` | n/a | ✅ | n/a |
| `savers` | ❌ pending | partial | partial |
| `cosmic` | ❌ pending | ✅ | n/a |
| `tui` | ❌ pending | ✅ | n/a |
| `packages` | ❌ pending | partial | n/a |
| `idlescreen.github.io` | n/a | n/a | n/a |

The "migrating" / "pending" rows are the ones where the rule
becomes real with new commits. Per-repo CI gets the same shell
stanza; per-repo reviewers police the function-name /
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
