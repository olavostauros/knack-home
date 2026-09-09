# scratchpad

Living state. Updated as work happens, not at the end.

## Current work — KnickKnackLabs/sessions#116

`sessions list --all` was capped at 20, so a corpus count silently returned 20.
Assigned by knick in [[work-queue]] on `knick/queue-sessions-116` (`5684522`).

**Status: fix complete and green, BLOCKED before the PR. Needs the owner.**

- Clone `~/agents/knack/sessions`, branch `knack/list-all-lifts-limit`,
  commit `7ac3f9b` — signed, authored knack, cut fresh from `upstream/main`
  (`b4d1e83`), a sibling of #138 and not a chain.
- **`7ac3f9b` is held unpushed.** `git push` to `knack-oikos/sessions` was
  rejected: the PAT carries `public_repo` only, and the branch republishes
  `.github/workflows/test.yml` because the fork's default branch is 25 commits
  behind the upstream base. Not a credential fault — `admin`/`push` on the fork
  both measured true.
- The two ways out (token scope, or advancing the fork's default branch) are
  the owner's. Reported and waiting; do not route around either.

### What is already proven

- Reproduced on 25 fixture sessions: `--all --json` returned 20 of 25.
- New case `list --all lifts the default limit` failed against unfixed code
  first (`expected all 24 sessions, got 20`), with `.mise/tasks/list` confirmed
  unmodified at that moment. After the fix, 25 of 25 and `list.bats` 28/28.
- Gates: `lint:python` clean, `codebase lint` 19/19, `git diff --check` clean,
  README regenerated to 393/387.

### Next session, in order

1. Ask the owner for the fork sync (narrower than widening token scope), then
   push `7ac3f9b` and open the PR against `KnickKnackLabs/sessions`.
2. PR body must mention `.mise/tasks/ps` as an observation only — same
   asymmetry, deliberately out of scope, let the maintainer ask.
3. Then set the queue entry to `pr-open` with the link.

## Standing hazards worth remembering

- **Do not trust a green `readme build --check` in `sessions`.** The repo's own
  pin (0.1.1) no-ops and reports success. Use `shiv:readme@0.3.4` and prove the
  checker is sensitive with a sentinel. Written up in [[mise-gotchas]].
- **`test/ci-cache.bats` in `sessions` reads your branch name.** 9 of 11 cases
  fail on any branch not literally named `main`. Not yours; do not chase it.
- Upstream CI on `sessions` is red at `b4d1e83` itself, failing in "Set up
  mise" before any test runs — so CI is not currently a signal there.
