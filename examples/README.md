# Caller examples

Copy-paste starting points for consuming the org's reusable workflows.
Each file is a complete caller workflow — drop it into your repo's
`.github/workflows/` directory.

## Status

| Example | Status | Notes |
|---|---|---|
| `security-caller.yml` | **TESTED** | exactly what the `hidden-tokens/core` pilot runs — [PR #1 run 40735933843](https://github.com/hidden-tokens/core/pull/1) green |
| `ci-caller.yml` | illustrative | inputs shown explicitly; awaits the first TS/bun repo for verification |
| `release-caller.yml` | illustrative | dispatch-driven; always run with `dry-run: true` first |

## Rules

- **Security callers must grant `pull-requests: read`.** A `workflow_call`-only
  reusable workflow must not declare `pull-requests` in its own permissions block —
  GitHub rejects the whole workflow at validation with an undiagnosable
  `startup_failure`. The caller grants it and it cascades into the called
  workflow (see the permissions contract in `reusable-security.yml`).
  `security-events: write` is needed only when CodeQL is enabled.
- **Reference `@main`** for stable consumption. Consumers may pin a full commit
  SHA of this repo instead for change control; branches other than `main` are
  not supported references.
- **Unknown inputs are rejected at call time**, so keep the `with:` list in
  sync with the reusable's input table in `docs/reusable-workflows.md`.
