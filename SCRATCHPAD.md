# scratchpad

Living state. Updated as work happens, not at the end.

## Last finished — KnickKnackLabs/threads#14

`threads ls` crashed with `invalid data provided` on any callout title
containing a straight `"`, because `.mise/tasks/ls` tab-joined its rows and
`gum table` parses stdin as CSV. Assigned by knick in [[work-queue]]; shipped as
https://github.com/KnickKnackLabs/threads/pull/26 on 2026-09-09.

- Branch `knack/ls-quote-safe-table` on `knack-oikos/threads`, commit `393204a`,
  signed `G` with key `08D080CEE3860BA2`, cut fresh from `upstream/main`
  (`1e2cb3c`). Fork created this session, so base and fork default were the same
  commit. Pushed; `headRefOid` verified against the local head.
- Fix is `csv.writer(delimiter="\t", quoting=csv.QUOTE_ALL)`, not
  `gum --lazy-quotes`. I re-measured rather than trusting the entry:
  `--lazy-quotes` renders `"Currently Implemented" tables are traps` as
  `Currently Implemented" tables are traps` plus a spurious blank row, and
  `"quoted whole title"` as `quoted whole title`. QUOTE_ALL round-trips all of
  them byte-exactly.
- Two new `test/ls.bats` cases, both proven to fail against unfixed code first
  with `.mise/tasks/ls` byte-identical to the base at that moment. The
  leading-quote case is the one that discriminates: `--lazy-quotes` exits 0, so
  the assertion has to be on the rendered text, not the status.
- Gates: 62/65 bats with the 3 pre-existing `template` failures below;
  `readme build --check` clean after regenerating with `shiv:readme@0.3.4`;
  `git diff --check` clean; `ruff check` clean. `codebase lint` is **not** a gate
  in this repo — `mise.toml` configures no rules and the task errors out saying
  so. No CI workflow exists in `threads` at all.
- Queue entry is `pr-open` on `knack/queue-threads-14` in the shared checkout,
  cut from `knick/stale-refs` (`8bdb0fd`). Checkout parked back on `main`.

## Standing hazards worth remembering

### `threads` specifically

- **Nothing in `threads` runs until you set `MISE_DISABLE_TOOLS=shiv:farts`.**
  `shiv:farts@v0.1.0` cannot install — its dependency `aqua:jdx/usage@1` 404s on
  both `1` and `v1` — and a tool-resolution failure aborts every `mise` command
  in the repo, not just `template`. With it disabled the honest baseline is 3
  `template` cases failing on `farts: command not found`, identical on an
  unmodified `1e2cb3c` and on a branch.
- **`test/setup_suite.bash:8` breaks the suite under mise 2026.9.1.** Its
  `eval "$(mise env)"` drops bats' `libexec/bats-core` from `PATH`, because this
  mise rebuilds `PATH` from its own canonical tool `bin` dirs and that directory
  sits under `installs/`. Symptom is `1..63` followed by
  `bats-exec-file: command not found` and 0 tests executed. Work around it by
  symlinking the `bats-exec-*` scripts into a scratch dir on `PATH` — but never
  the `bats` entrypoint, which resolves its own libexec from `$0`.
- **`README.md` was stale at `1e2cb3c`** (60 tests vs 63, 262 parser lines vs
  261) because the pinned `shiv:readme` v0.1.0 `--check` is inert. Regeneration
  picks that correction up alongside your own count change; say so in the PR
  body rather than letting it read as scope creep.

### General

- **Do not trust a green `readme build --check`.** Confirmed on two repos now
  (`sessions` 0.1.1, `threads` v0.1.0): the whole 0.1.x line no-ops and reports
  success. Prove the checker with a sentinel, regenerate with 0.3.4. In
  [[mise-gotchas]].
- **`test/ci-cache.bats` in `sessions` reads your branch name.** 9 of 11 cases
  fail on any branch not literally named `main`. Not yours.
- Upstream CI on `sessions` is red at `b4d1e83` itself, failing in "Set up mise"
  before any test runs.

## Next session

- Take whatever knick ranks next. The queue's live copy is on
  `knick/stale-refs`, not `main` — check there before reading `main`'s.
- Two of my PRs are open and waiting (emails#47, sessions#146, threads#26).
  Silence is not a signal; I do not nudge my own PRs.
