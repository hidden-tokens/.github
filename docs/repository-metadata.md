# Repository metadata — what's here and what's deliberately not

This file records the metadata decisions for `hidden-tokens/.github` so the
absence of a file is a decision, not an oversight.

## Present

| File | Scope | Notes |
|---|---|---|
| `README.md` | org default | This repo's own overview; the org profile lives in `profile/README.md` |
| `SECURITY.md` | org default | Private reporting to info@hiddentokens.com — never public issues |
| `SUPPORT.md` | org default | Support channels, no confidential documents |
| `CONTRIBUTING.md` | org default | Contribution flow |
| `CODE_OF_CONDUCT.md` | org default | Community standards |
| `ISSUE_TEMPLATE/` | org default | Bug/feature forms with the privacy checklist |
| `PULL_REQUEST_TEMPLATE.md` | org default | Includes the privacy checklist |
| `.github/CODEOWNERS` | **this repo only** | Not inherited by other org repos — see the file header |

## Deliberately absent

| File | Reason |
|---|---|
| `LICENSE` | **Decision made (2026-10-28): stay closed.** No license is added; the public repo is "all rights reserved" by default. Anyone wanting to reuse this org's material must ask. Revisit only if the org owner changes this decision. |
| `FUNDING.yml` | No funding accounts are in use. Add only when a real funding target exists. |
| `CITATION.cff` | Citation metadata is for research artifacts; a workflow platform repo has nothing citable. |
| `GOVERNANCE.md` | Governance is currently "the org owner decides" — written down here rather than in a formal file. Revisit if maintainers are added. |

## Org defaults vs. project-specific

- **Auto-discovered by GitHub** for every org repo (unless the repo provides
  its own copy): `profile/`, `ISSUE_TEMPLATE/`, `PULL_REQUEST_TEMPLATE.md`,
  `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`, `SUPPORT.md`.
- **Never auto-discovered**: workflows, `CODEOWNERS`, `dependabot.yml` — these
  require explicit adoption per repository (see
  [`docs/integration-guide.md`](integration-guide.md)).
