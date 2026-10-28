# Standards catalog

Real inventory of this repository — what exists, what it does, how it is
adopted. Verified 2026-10-28 against the working tree; the repo's own CI
(validation workflow) guards workflow files.

## Community standards (auto-discovered by all org repos)

| File | Purpose | Maintainer |
|---|---|---|
| `profile/README.md`, `profile/banner.svg` | Org landing page | org owner |
| `ISSUE_TEMPLATE/*.yml` | Bug + feature forms, privacy checklist | org owner |
| `PULL_REQUEST_TEMPLATE.md` | PR description template | org owner |
| `CONTRIBUTING.md` | Contribution flow | org owner |
| `CODE_OF_CONDUCT.md` | Community standards | org owner |
| `SECURITY.md` | Private vulnerability reporting (email + GitHub PVR) | org owner |
| `SUPPORT.md` | Support channels | org owner |

## Workflows (explicit adoption — callers required)

| File | Consumed via | Status |
|---|---|---|
| `.github/workflows/reusable-security.yml` | `examples/security-caller.yml` | **Run-verified** (core pilot, run 40735933843) |
| `.github/workflows/reusable-ci.yml` | `examples/ci-caller.yml` | Illustrative — awaits first code repo |
| `.github/workflows/reusable-release.yml` | `examples/release-caller.yml` | Illustrative — dry-run first |
| `.github/workflows/validation-actionlint.yml` | Self-hosted in this repo | **Run-verified** (run 40744114836) |

## Org repo configuration

| File | Purpose |
|---|---|
| `.github/dependabot.yml` | Weekly grouped Actions-pin updates |
| `.github/CODEOWNERS` | Review routing for this repo only |

## Documentation

| File | Covers |
|---|---|
| `docs/reusable-workflows.md` | Input tables + permissions contract for all reusables |
| `docs/integration-guide.md` | How to adopt (auto vs explicit, step-by-step, checklist) |
| `docs/release-conventions.md` | Versioning, reference strategy, breaking-change process |
| `docs/branch-protection.md` | Ruleset recommendations for `main`/`develop` |
| `docs/permissions-audit.md` | Trust/permission audit with residual risks |
| `docs/troubleshooting.md` | Known failure modes + diagnostics |
| `docs/repository-metadata.md` | Metadata decisions incl. deliberate absences |
| `docs/compatibility-matrix.md` | P2.1 audit — repo landscape + pilot selection |

## Known limitations

- CI and release reusables are untested against real repos (inputs mirror the
  audited design; verification pending first code-repo adoption).
- No Rust/Tauri CI profile yet — it is the primary product profile and is
  roadmap-priority for the first product repo adoption.
- The org holds 2 repositories; rollout scope is intentionally tiny until the
  org grows.

## Verification procedures

- Workflow files: `validation-actionlint.yml` (runs on every push/PR).
- Caller examples: linted by the same job; the security example is additionally
  run-verified via the pilot.
- Docs: links verified during the P8 final sweep.
