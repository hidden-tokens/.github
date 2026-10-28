# Troubleshooting reusable workflows

Field notes from real failures in this org, kept short on purpose.

## `startup_failure` with no logs or annotations

**Symptom:** a run completes in ~1s as `startup_failure`; the API returns 404
for its jobs, logs, and annotations.

**Cause:** GitHub rejected the workflow file at validation — it never ran, so
there is nothing to log. Two known triggers in this org:

1. A `workflow_call`-only reusable workflow declares `pull-requests` in its
   own `permissions:` block. **The caller grants it; the reusable must not
   declare it.** (Found 2026-10-28 — see `reusable-security.yml` header.)
2. A caller references an input the reusable doesn't define, or vice versa.

**Why the linters don't catch it:** actionlint and PyYAML both validate this
file as fine — the rejection is GitHub's stricter validation layer.

**How to diagnose when nothing is readable:** sequential run-bisect against a
same-repo test caller. Push variants of the workflow to a throwaway branch;
each push is a ~20-second answer. Method that found the permission bug:
sanity-echo → add the call → strip jobs → strip permissions one scope at a
time. ~20 minutes total. Keep the test caller out of the final merge.

## Trufflehog: `flag 'no-update' cannot be repeated`

The `trufflesecurity/trufflehog` action wrapper adds `--no-update` itself.
Passing it again in `extra_args` fails instantly. `extra_args` should carry
only your own flags, e.g. `--only-verified`.

## Dependency review fails on a private repo

`Dependency review is not supported on this repository...` — the dependency
graph + GHAS requirement. The `dependency-review` input defaults to `auto`
(runs on public repos only). Private + GHAS: pass `on`. Never want it: `off`.

## CodeQL job skipped on a repo that has code

`codeql-languages` defaults to empty (off). Pass e.g.
`codeql-languages: typescript,javascript`. On private repos CodeQL also
requires GitHub Advanced Security.

## "No commits between develop and <branch>" when opening a PR

Usually means the push hadn't landed when GitHub looked. Re-check with
`git ls-remote origin <branch>`; if the SHA is right but the error persists,
it can be API lag — retry the `gh pr create` before investigating further.

## Caller error vs. reusable error

- **Failure appears as a run named after YOUR caller workflow, status
  `startup_failure`, 0s** → the *caller* file or the call is malformed
  (permissions contract, unknown input).
- **Failure appears as a run named after the reusable** (e.g.
  `Security (reusable)`), with jobs and steps → the *reusable* ran and
  something inside it failed; read the failing step's log.
- The run list's "Triggered via" and the workflow name in
  `gh run list` distinguish the two cases.
