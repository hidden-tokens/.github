# Branch protection guidance

Policy recommendations for every hidden-tokens repository. Applying rulesets
is a **repository-admin (org-owner) action** in GitHub's settings UI — this
document is the checklist, not the change.

## The branching convention

```
feature/<topic> ──PR──▶ develop ──PR──▶ main
```

- `develop` is the integration branch (default branch on new repos).
- `main` is the release surface; reusable workflows are consumed from `@main`.
- Direct pushes to `main` never happen — not even by the owner.
- Never force-push; never delete `main` or `develop`.

## Recommended ruleset for `main` (org owner applies)

In *Settings → Rules → Rulesets → New branch ruleset* targeting `main`:

| Rule | Value | Why |
|---|---|---|
| Require a pull request before merging | on | The cut PR is the release review |
| Required approvals | 0–1 | Single maintainer: 0 is honest; 1 blocks solo merges |
| Disallow force pushes | on | Immutable history on the release surface |
| Disallow deletions | on | Obvious |
| Require status checks | on | Which check to require depends on the repo — see below |
| Require branches to be up to date | off | Cuts merge from `develop` after its own checks; extra freshness gates add noise |

### Which status checks to require, per repo

- A repo consuming the **security** caller: require `security` (the caller
  workflow's name). Note the check appears as `security / <job name>` —
  configure the job-level checks, not the run name, after the first PR run
  has populated the list.
- A repo consuming the **CI** caller: require `ci / <job name>`.
- This platform repo: require `Workflow validation / actionlint`.

## Recommended ruleset for `develop`

Same ruleset minus required status checks (or with the repo's caller checks
required, if you want green-before-integrate). Keeping `develop` lighter is
fine: `main` is the protected surface.

## Avoid blocking yourself

- Do not require approvals > the number of people who can approve. With one
  maintainer, requiring 1 approval means every merge needs the `--force`-ish
  admin override or self-approval via the ruleset's bypass list. Add the
  owner as an explicit **bypass actor** on the ruleset instead of lowering
  rules silently.
- Do not require checks that only run on some events (e.g. dependency review
  is PR-only and auto-skips on private repos — a flaky require target).
- After enabling a ruleset, make one throwaway PR to verify the rules behave
  before relying on them.

## Where policy ends and privilege begins

Everything above is documented policy. The `protect-main` ruleset is now
**applied** (2026-10-28) on this repo and on `core` via the rulesets API —
enforcement `active`, targeting `refs/heads/main`, verified by an actual
rejected direct push. New org repositories get the same ruleset at adoption
time (see [`docs/integration-guide.md`](integration-guide.md)).
