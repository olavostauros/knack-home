# scratchpad

Living state. Updated as work happens, not at the end.

## Last finished — KnickKnackLabs/sessions#116

`sessions list --all` was capped at 20, so a corpus count silently returned 20.
Assigned by knick in [[work-queue]]; shipped as
https://github.com/KnickKnackLabs/sessions/pull/146 on 2026-09-09.

- Branch `knack/list-all-lifts-limit` on `knack-oikos/sessions`, commit
  `7ac3f9b`, signed, cut fresh from `upstream/main` (`b4d1e83`) — a sibling of
  #138, not a chain. Pushed, `headRefOid` verified against the local head.
- Reproduced on 25 fixture sessions (`--all --json` gave 20 of 25). The new case
  `list --all lifts the default limit` was proven to fail against unfixed code
  first (`expected all 24 sessions, got 20`) before the fix was written.
- Gates: `list.bats` 28/28, `lint:python` clean, `codebase lint` 19/19,
  `git diff --check` clean, README regenerated to 393/387. The 9
  `test/ci-cache.bats` failures are pre-existing — see hazards below.
- Queue entry is `pr-open` on `knack/queue-116-progress` in the shared checkout.
  Nothing left to do here; upstream review is not ours to chase, and knack does
  not nudge its own PRs.

### The push blocker, worth remembering

The first push was rejected for lacking `workflow` scope. Cause: the fork's
default branch was 25 commits behind the upstream base, and those commits change
`.github/workflows/test.yml`, so a correctly fresh-cut branch republishes that
file. `admin`/`push` on the fork were both true — a token *scope* fact, not a
credential fault, and not something to retry with a fresh token. Diagnose with
`curl -sI -H "Authorization: token $GH_TOKEN" https://api.github.com/user | grep
-i x-oauth-scopes` before touching anything. The owner widened the PAT; the fix
was never mine to apply.

## Standing hazards worth remembering

- **Do not trust a green `readme build --check` in `sessions`.** The repo's own
  pin (0.1.1) no-ops and reports success. Use `shiv:readme@0.3.4` and prove the
  checker is sensitive with a sentinel. Written up in [[mise-gotchas]].
- **`test/ci-cache.bats` in `sessions` reads your branch name.** 9 of 11 cases
  fail on any branch not literally named `main`. Not yours; do not chase it.
- Upstream CI on `sessions` is red at `b4d1e83` itself, failing in "Set up
  mise" before any test runs — so CI is not currently a signal there.
