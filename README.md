# .github — hidden-tokens organization defaults

This repository holds the organization-wide defaults for all [hidden-tokens](https://github.com/hidden-tokens) public repositories:

| File | What it does |
|---|---|
| [`profile/README.md`](profile/README.md) | The organization profile page |
| [`ISSUE_TEMPLATE/`](ISSUE_TEMPLATE/) | Issue forms for bug reports and feature requests, shared across all repos |
| [`PULL_REQUEST_TEMPLATE.md`](PULL_REQUEST_TEMPLATE.md) | Default PR description, including the privacy checklist |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | How to contribute |
| [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md) | Community standards |
| [`SECURITY.md`](SECURITY.md) | How to report a vulnerability |
| [`SUPPORT.md`](SUPPORT.md) | Where to get help |
| [`.github/workflows/reusable-ci.yml`](.github/workflows/reusable-ci.yml) | Reusable CI pipeline — lint, typecheck, test, build |
| [`.github/workflows/reusable-security.yml`](.github/workflows/reusable-security.yml) | Reusable security pipeline — secret scan, dependency review, optional CodeQL |
| [`.github/workflows/reusable-release.yml`](.github/workflows/reusable-release.yml) | Reusable release pipeline — git-cliff changelog, tag guard, GitHub Release |

Individual product repositories may override any of these by providing their own copy.

## Reusable workflows

Unlike the community files above, reusable workflows do **not** apply automatically — each repository must call them from its own `.github/workflows/`. See the header of each workflow file for the exact `uses:` invocation, per-repo inputs (`package-manager`, `working-directory`, `codeql-languages`, …), and tunable stages.

## The privacy rule

Every template enforces the same boundary: **no real names, identifiers, amounts, confidential documents, or token↔value mappings in public issues, pull requests, or code.** Test data uses fictional values. This is what the product stands for — it applies to its development too.

---

**Hidden Tokens** — [hiddentokens.com](https://hiddentokens.com/) · AI does the work. It never knows who.
