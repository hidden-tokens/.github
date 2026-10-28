# Integration guide — adopting the org standards

For any repository in the [hidden-tokens](https://github.com/hidden-tokens) org.
Adoption takes ~10 minutes for the workflows; the community files are already
applied.

## What GitHub applies automatically

These come from the special `.github` repository and need **zero action** —
unless a repo adds its own copy, which overrides the org default:

| Applies automatically | Notes |
|---|---|
| Org profile (`profile/README.md`) | The org landing page |
| `ISSUE_TEMPLATE/` | Bug + feature forms, with the privacy checklist |
| `PULL_REQUEST_TEMPLATE.md` | PR description incl. privacy checklist |
| `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md` | Community standards |
| `SECURITY.md`, `SUPPORT.md` | Reporting + support channels |

## What requires explicit integration

| Feature | How to adopt |
|---|---|
| Reusable CI / security / release workflows | Add a caller workflow — see [`examples/`](../examples/) |
| `CODEOWNERS` | Copy from this repo's `.github/CODEOWNERS` and adjust owners |
| Dependabot | Add a `.github/dependabot.yml` (see this repo's) |
| Branch protection / rulesets | Repo settings — see [`docs/branch-protection.md`](branch-protection.md) |

## Adopting the reusable workflows (step by step)

1. **Security** (recommended for every repo on day one):
   copy [`examples/security-caller.yml`](../examples/security-caller.yml) into
   your repo's `.github/workflows/security.yml`. It is the exact file the
   pilot runs — no edits needed unless you enable CodeQL.
2. **CI** (code repos): copy [`examples/ci-caller.yml`](../examples/ci-caller.yml)
   and set `package-manager` (`bun` / `pnpm` / `npm`) and stage toggles.
3. **Release** (repos that tag releases): copy
   [`examples/release-caller.yml`](../examples/release-caller.yml); always
   dispatch with `dry-run: true` first.
4. Open a PR with the new file(s) — the org's validation workflow cannot see
   your repo, so check the run of your caller in your repo's Actions tab.
5. Merge; the runs appear on every push/PR from then on.

## Overrides and limitations

- Any repo file (e.g. your own `SECURITY.md`) overrides the org default —
  keep the org content and append rather than replace where possible.
- Dependency review auto-skips on **private** repos without GitHub Advanced
  Security (`dependency-review: auto` default). Force with `on` only if the
  repo has GHAS.
- CodeQL runs only if you pass `codeql-languages`; on private repos it needs
  GHAS.
- The reusable workflows run on `ubuntu-latest` by default (`runs-on` input
  overrides). Rust/Tauri product repos: CI coverage for cargo/tauri is on the
  roadmap as the primary product profile — ask before hand-rolling.
- The `pull-requests: read` permission grant in the security caller is
  **required** — see the contract in
  [`docs/reusable-workflows.md`](reusable-workflows.md).

## Adoption checklist

- [ ] Caller workflow(s) added and merged
- [ ] First run green in the repo's Actions tab
- [ ] Branch protection on `main` (rulesets) configured
- [ ] Dependabot config added
- [ ] `CODEOWNERS` added if the repo has multiple touchpoints
- [ ] Repo README mentions which workflows it consumes
