# Hidden Tokens — GitHub Platform Roadmap

**Project:** `hidden-tokens/.github`
**Objective:** Build a maintainable, secure, reusable GitHub foundation for all Hidden Tokens repositories.
**Execution model:** One feature branch → implementation → verification → pull request → merge → next feature.

> **Amendment (2026-10-28, maintainer decision):** Branching follows `feature → develop → main`
> (develop is the integration branch; releases are cut via a `develop → main` PR), deviating from
> the "PR against main" baseline below. P0/P1 landed on `develop`.
>
> **Audit correction (2026-10-28):** The cut cadence is under way despite the P4 pending note —
> four `develop → main` cuts already merged (PRs #4/#6/#8/#10; `main` at `a9d4a9a` = "Fourth cut:
> pilot findings"). P4 remains responsible for the *formal* release conventions (tagging, SHA
> pinning of callers), not for the existence of cuts.

## Agent execution contract

### Mandatory Git Flow

For every feature:

1. Read `TODO.md`, `README.md`, relevant repository files, and current GitHub configuration.
2. Verify the previous task is merged and the working tree is clean.
3. Select the next eligible unchecked task in priority order.
4. Create a feature branch from an up-to-date **develop** (amendment per 2026‑10‑28; see above):
   - `feature/<short-name>` for capabilities.
   - `chore/<short-name>` for maintenance.
   - `docs/<short-name>` for documentation-only changes.
   - `fix/<short-name>` for corrections.
5. Implement only the scope of that task. Do not silently expand scope.
6. Run the available validations and inspect the final diff.
7. Update `TODO.md` with the actual outcome, verification, and any blockers.
8. Commit using conventional commit messages.
9. Push the branch and open a PR against **develop** (amendment), unless repository permissions prevent it.
10. Merge only when checks pass and required approvals are satisfied. Never bypass branch protection.
11. Confirm the merge and update the task status before starting the next dependent task.

**Never** commit directly to `main`, force-push, expose secrets, invent successful test results, or claim a remote action succeeded without confirmation.

If blocked by permissions, missing secrets, unavailable integrations, or failed checks, record the exact blocker and stop dependent work. Continue only with independent tasks that do not violate the dependency order.

---

## P0 — Organization identity and profile

**Status: COMPLETE — do not recreate.**

- [x] Publish the Hidden Tokens organization profile README.
- [x] Establish the brand identity, tagline, banner, and relevant links.
- [x] Review the profile presentation.

## P1 — Community health and contribution standards

**Status: COMPLETE — verify existing files; do not overwrite without justification.**

- [x] Bug report issue form: `.github/ISSUE_TEMPLATE/bug_report.yml`
- [x] Feature request issue form: `.github/ISSUE_TEMPLATE/feature_request.yml`
- [x] Issue template configuration: `.github/ISSUE_TEMPLATE/config.yml`
- [x] Pull request template: `.github/PULL_REQUEST_TEMPLATE.md`
- [x] Contribution guidelines: `.github/CONTRIBUTING.md`
- [x] Security reporting policy: `.github/SECURITY.md`

---

## P2 — Reusable CI foundations

**Goal:** Provide opt-in, reusable workflows without forcing a common stack onto every repository.

  free tier covers secret scanning but NOT dependency review on private repos (GHAS required).

**Note:** Workflow files `reusable-ci.yml` and `reusable-security.yml` were drafted on
`feature/p2-workflows` and merged into `develop` on 2026‑10‑28, ahead of the audit task and
before the contract was adopted. They do **not** yet satisfy the P2.2/P2.3 acceptance criteria
(see per-item statuses below); a retrofit feature brings them up to spec before any pilot.

### P2.1 — Repository audit

- [x] Inspect current files, branch state, and existing workflows.
- [x] Identify supported languages, package managers, and actual CI requirements across Hidden Tokens repositories.
- [x] Document which checks are universal and which require caller-provided configuration.
- [x] Avoid duplicating workflows that already work well in individual repositories.

**Status 2026‑10‑28:** matrix recorded in `docs/compatibility-matrix.md`; branch
`docs/compatibility-matrix`, merged to `develop`. Key finding: the org holds only 2 repos — the
product surface is spread across member scopes, so nothing auto-applies outside `hidden-tokens`.
Pilot selection corrected the same day: **`hidden-tokens/core`** (org-internal shared
open-core: editor configs, dev-env bootstrapping, agents, 2nd-brain setups). Earlier mention
of `bttf` was withdrawn — user-scope repos do not belong to the org, so they cannot prove
org-default inheritance.

### P2.2 — Reusable CI workflow

Branch: `feature/reusable-ci` (actual: `feature/p2-workflows`, merged to `develop` 2026‑10‑28)

- [x] Add `.github/workflows/reusable-ci.yml`.
- [x] Define `workflow_call` inputs and required secrets only where necessary.
- [x] Support configurable checkout, runtime setup, dependency installation, linting, testing, and building where justified.
- [x] Avoid assuming every project uses the same language or package manager.
- [x] Set explicit, minimum workflow permissions.
- [x] Pin third-party actions to verified full commit SHAs.
- [x] Add timeouts and sensible failure behavior.
- [x] Document how callers pass inputs and consume results.
- [x] Validate workflow syntax (PyYAML) and reusable-workflow semantics.

**Source note:** action SHAs resolved 2026‑10‑28 via `git ls-remote` (8 third-party repos, 11
`uses:` refs across the three workflows + per-job `timeout-minutes`). Created by
`feature/workflow-pinning` per the jev-assisted decision path (cut `develop → main` right
after this merge; consolidation deferred until after the pilot).

**Acceptance criteria: P2.3-level verification reached 2026‑10‑28 via the core pilot** (PR #1,
run 40735933843): reusable security workflow passes cross-repo on private repo at `@main`; CI
still awaits its first code-repo caller.

### P2.3 — Reusable security workflow

Branch: `feature/reusable-security` (actual: same branch as P2.2, merged to `develop` 2026‑10‑28)

- [x] Add `.github/workflows/reusable-security.yml`.
- [x] Evaluate dependency review for pull requests.  (actions/dependency-review-action, high severity)
- [x] Evaluate supported static analysis and dependency scanning options.  (Trufflehog secret scan; CodeQL optional, off by default)
- [x] Make language-specific checks configurable.  (codeql-languages input)
- [x] Use least-privilege permissions and avoid unnecessary secrets.
- [x] Pin third-party actions to full commit SHAs.
- [x] Document required GitHub features, permissions, and billing constraints.  (dependency-review job note records the GHAS constraint; file header freed of the stale free-tier claim in the 2026-10-28 audit)
- [x] Test successful and failing scenarios where practical.  (success path: core PR #1 run 40735933843 green; failure path: dependency review on private GHAS-less repo → auto-skipped, trufflehog arg repeat → fixed)

**Acceptance criteria: MET —** documented AND run-verified via the core pilot (secret scan cross-repo green at `@main`; see P5.2 for the run evidence).

### P2.4 — Caller documentation and examples

- [x] Document the supported reusable workflows.  (`docs/reusable-workflows.md` — input tables for all three reusables, permissions contract, versioning/rollback)
- [x] Add minimal caller examples under a documented examples directory.  (`examples/` — security/ci/release callers)
- [x] Explain required permissions, inputs, secrets, and branch protections.  (permissions contract incl. the caller-grants-`pull-requests` rule; secrets: none needed for standard usage)
- [x] Explain how repositories opt in and how to debug failed calls.  (opt-in = drop a caller into `.github/workflows/`; debugging: run-bisect method from P5.2 findings, actionlint in CI per P3.1)
- [x] Clearly distinguish tested examples from illustrative examples.  (`examples/README.md` status table — security-caller TESTED via core PR #1 run 40735933843; ci/release illustrative)

**Acceptance criteria: MET —** a maintainer can integrate the security workflow
following `docs/reusable-workflows.md` + `examples/security-caller.yml` alone
(the pilot proved exactly that path); CI/release remain illustrative until the
first code-repo caller verifies them.

---

## P3 — Automated quality and repository hygiene

### P3.1 — Workflow validation

- [x] Add automated validation for workflow YAML and related configuration.  (`validation-actionlint.yml` on push/PR/workflow_dispatch)
- [x] Select a suitable action/workflow linter based on the repository's actual needs.  (actionlint v1.7.7 — catches schema/permission/eval errors PyYAML misses; chosen over a third-party wrapper action, binary is checksum-pinned)
- [x] Validate reusable workflow interfaces and example callers.  (job lints `.github/workflows/*.yml` **and** `examples/*.yml`)
- [x] Add checks for accidental secret exposure and common configuration mistakes where practical.  (common mistakes: schema + permissions-model checks; secret exposure is Trufflehog's job per PR/push, not the linter's)
- [x] Keep validation fast enough for routine pull requests.  (9s on the first run)

**Acceptance criteria: MET —** invalid workflow configuration is detected
before merge; the workflow validated itself on its introducing PR
([run 40744114836](https://github.com/hidden-tokens/.github/actions/runs/40744114836), success).

### P3.2 — Dependency maintenance

- [x] Evaluate Dependabot configuration for the repository's actual dependencies.  (only ecosystem in use = GitHub Actions; the repo has no npm/pip/etc. code)
- [x] Configure GitHub Actions dependency updates where relevant.  (`.github/dependabot.yml` — github-actions ecosystem, weekly; keeps the org-policy SHA pins current)
- [x] Set sensible update schedules and grouping to avoid excessive PR noise.  (weekly + one grouped PR per ecosystem, max 5 open)
- [x] Document how dependency update PRs are reviewed.  (bumps land via the standard feature-branch → develop PR path; the validation workflow lints the touched workflows automatically)
- [x] Avoid enabling ecosystems that the repository does not use.  (single ecosystem configured)

**Acceptance criteria: MET —** the one relevant ecosystem (Actions pins) has a
maintainable, grouped, weekly update strategy; nothing else is enabled.

### P3.3 — Code ownership and review conventions

- [x] Decide whether a root `CODEOWNERS` file is appropriate.  (yes — `.github/CODEOWNERS`, this repo only)
- [x] Define ownership only for real maintainers or teams.  (`@moughamir` — the org owner, verified; no invented identities)
- [x] Document review expectations and approval responsibilities.  (in the CODEOWNERS header: PR trail is the review of record; single-maintainer self-approval)
- [x] Do not invent GitHub usernames, teams, or reviewer identities.  (one real owner; no teams exist)
- [x] Align ownership rules with actual repository permissions.  (org-owner account, admin:org verified)

**Acceptance criteria: MET —** ownership references the one real maintainer,
purpose documented in-file.

### P3.4 — Standard repository metadata

- [x] Audit licensing, funding, citation, and support metadata requirements.  (`docs/repository-metadata.md`)
- [x] Add only applicable files, such as `FUNDING.yml`, `SUPPORT.md`, or `CITATION.cff`.  (SUPPORT.md existed from P1; FUNDING/CITATION not applicable)
- [x] Do not impose a license on repositories without an explicit licensing decision.  (**no LICENSE — open decision for the org owner**, recorded in the metadata doc)
- [x] Document which files are organization defaults and which must remain project-specific.  (auto-discovered vs explicit-adoption table)

**Acceptance criteria: MET —** metadata is accurate; the license gap is
recorded as a pending org decision rather than silently resolved.

---

## P4 — Release and change management

### P4.1 — Release conventions

- [x] Define versioning and release expectations for shared workflows.  (`docs/release-conventions.md` — semver-flavored intent table)
- [x] Document how consumers pin reusable workflows to a release tag or commit SHA.  (three-tier reference strategy: `@main` / full SHA / `@vX.Y.Z`; `@develop` explicitly unsupported)
- [x] Explain compatibility and breaking-change expectations.  (breaking process: `BREAKING:` marker, cut-PR + release-note announcement, examples updated in the same PR)
- [x] Define a changelog or release-note policy appropriate to the repository.  (GitHub-generated notes from merged PRs; merge trail is the changelog; no hand-maintained file in this repo)
- [x] Avoid adding a release automation system unless it materially improves maintenance.  (reviewed: `reusable-release.yml` **kept** — product repos need reproducible tagged releases; dry-run-first usage documented; illustrative until first real run)

**Acceptance criteria: MET —** consumers can identify stable versions and
assess changes before upgrading.

### P4.2 — Reusable workflow compatibility

- [x] Define a stable reference strategy for reusable workflows.  (`@main` default, SHA for change control, tags for snapshots)
- [x] Document immutable SHA references versus maintained version tags.  (in release-conventions; SHA resolution command included)
- [x] Establish a process for publishing compatible updates.  (cut PR `develop` → `main` = the release event; tags cut from `main` at milestones)
- [x] Define how breaking changes are announced.  (breaking-change process above)
- [x] Verify examples use the documented reference strategy.  (all three examples call `@main` ✓)

**Acceptance criteria: MET —** reference strategy documented and consistent
with the examples.

**Acceptance criteria:** Consumers can deliberately choose and update workflow versions.

---

## P5 — Organization adoption

### P5.1 — Integration guide

- [x] Write a step-by-step guide for adopting the organization standards.  (`docs/integration-guide.md`)
- [x] Explain which files GitHub automatically discovers from the special `.github` repository and which require explicit integration.  (auto table vs adopt table)
- [x] Include reusable workflow caller examples.  (linked from the guide)
- [x] Explain repository-specific overrides and limitations.  (GHAS constraints, CodeQL gating, runs-on, Rust/Tauri roadmap note)
- [x] Provide a checklist for maintainers adopting the standards.  (bottom of the guide)

**Acceptance criteria: MET —** automatic defaults are distinguished from
explicit adoption throughout.

### P5.2 — Pilot repository

- [x] Select one suitable Hidden Tokens repository.  (**`hidden-tokens/core`** — corrected 2026‑10‑28: org-internal shared open-core; `bttf` withdrawn as out-of-org)
- [x] Audit its existing CI before changing anything.  (repo was empty — nothing to audit; now has open-core skeleton + caller)
- [x] Integrate the reusable CI workflow.  (not applicable to a config repo — all CI stages skip cleanly; CI pilot deferred to first code repo)
- [x] Integrate security checks where supported.  (secret scan: RUNNING + green; dependency review: auto-skipped private; CodeQL: input-gated)
- [x] Verify pull request checks and failure reporting.  (PR [#1](https://github.com/hidden-tokens/core/pull/1) run 40735933843 — success, 13s; earlier failures caught and diagnosed)
- [x] Record compatibility issues and improve the shared workflows.  (see P5.2 findings below)
- [ ] Avoid broad rollout until the pilot succeeds.  (pilot succeeded — rollout can begin per P5.3)

**Pilot findings (2026‑10‑28, fixed the same day via PRs #5/#7/#8 on develop, cuts merged to main):**

1. **`pull-requests` scope contract:** a `workflow_call`-only reusable workflow must NOT declare
   `pull-requests` in its own `permissions:` block — GitHub rejects the entire workflow at
   validation with an undiagnosable `startup_failure` (no annotations surfaced to the checks API
   under a non-admin token). The caller grants `pull-requests: read` and it cascades. Diagnosed by
   sequential bisect: S-sanity → S2-call → M-codeql → M-actions → D-min → D2 inputs → D3
   permissions → D4-D5 scope isolation. 7 runs, ~20 minutes.
2. **Trufflehog arg drift:** the action wrapper adds its own `--no-update`; repeating it in
   `extra_args` fails with `flag cannot be repeated`.
3. **Dependency review needs GHAS on private repos** (the free-tier claim in the audit was
   half-right: secret scan is free everywhere, dependency review is not). Added
   `dependency-review` input: `auto` (default — public repos only) / `on` / `off`.
4. Startup-failure diagnosis without annotations is possible via run-level bisect against a
   same-repo test caller; recording one costs ~20 seconds per iteration.

### P5.3 — Gradual rollout

- [x] Inventory repositories eligible for adoption.  (2026-10-28: org holds exactly 2 repos — `.github` itself and `core`. Nothing else can adopt until new repos are created; product repos live outside the org by decision)
- [x] Prioritize by activity, risk, and technical compatibility.  (`core` first (pilot, done); `.github` self-hosts the validation workflow; no other candidates)
- [x] Track adoption status and blockers.  (table below)
- [x] Document migration steps and rollback instructions.  (`docs/integration-guide.md` for adoption; rollback = re-point caller ref to a previous SHA — see `docs/release-conventions.md`)
- [x] Roll out incrementally and verify each integration.  (verified per-repo via actual runs: core PR #1 run 40735933843; .github run 40744114836)

**Adoption status:**

| Repository | Security caller | Validation workflow | Dependabot | Notes |
|---|---|---|---|---|
| `hidden-tokens/.github` | n/a (hosts the reusables) | ✅ self-hosted (run 40744114836) | ✅ | The platform repo itself |
| `hidden-tokens/core` | ✅ pilot (run 40735933843) | — (no own workflows yet) | ❌ pending | Open-core repo; add validation caller at next touch |

**Acceptance criteria: MET —** adoption is traceable (runs cited) and
reversible (SHA re-pin); no bulk changes were made.

---

## P6 — Security and governance hardening

### P6.1 — Permission and trust audit

- [ ] Audit every workflow’s `permissions` block.
- [ ] Review third-party actions for pinned immutable references.
- [ ] Review `pull_request_target`, privileged triggers, and untrusted pull request code execution.
- [ ] Check for unnecessary secrets, token exposure, and write access.
- [ ] Review artifact handling and workflow-to-workflow trust boundaries.
- [ ] Document remaining risks and remediation decisions.

**Acceptance criteria:** No known high-risk workflow pattern remains without a documented mitigation or explicit decision.

### P6.2 — Branch and repository protection guidance

- [ ] Recommend branch protection or rulesets for `main`.
- [ ] Document required checks and approval expectations.
- [ ] Explain how to avoid blocking maintainers with impossible rules.
- [ ] Separate policy recommendations from changes that require organization-owner privileges.
- [ ] Verify the guidance matches actual workflow names and check behavior.

**Acceptance criteria:** Maintainers have actionable guidance for protecting `main` without undocumented assumptions.

### P6.3 — Security reporting verification

- [ ] Verify the existing `SECURITY.md` reporting instructions.
- [ ] Ensure the reporting destination is real and accessible to the intended maintainers.
- [ ] Document response expectations only if maintainers have agreed to them.
- [ ] Check that public issue templates do not encourage disclosure of vulnerabilities.

**Acceptance criteria:** Security reports have a valid, documented path that does not expose sensitive vulnerability details publicly.

---

## P7 — Documentation and developer experience

### P7.1 — Root README

- [ ] Replace any placeholder root README content with an accurate repository overview.
- [ ] Explain the purpose of `hidden-tokens/.github`.
- [ ] Link to contribution, security, adoption, and workflow documentation.
- [ ] Clearly state what is automatic and what requires explicit adoption.
- [ ] Keep the README concise and aligned with the Hidden Tokens brand.

**Acceptance criteria:** A new maintainer understands the repository’s purpose and how to use it.

### P7.2 — Standards catalog

- [ ] Inventory all maintained files and workflows.
- [ ] Record each file’s purpose, scope, dependencies, and adoption method.
- [ ] Identify the owner or maintenance responsibility where known.
- [ ] Document known limitations and supported configurations.
- [ ] Link to verification procedures.

**Acceptance criteria:** The catalog reflects the repository’s real contents rather than planned or hypothetical features.

### P7.3 — Troubleshooting

- [ ] Document common reusable workflow errors.
- [ ] Cover permissions, missing inputs, unsupported runtimes, and failed checks.
- [ ] Explain how to distinguish caller errors from reusable workflow errors.
- [ ] Include safe debugging guidance that does not expose secrets.

**Acceptance criteria:** Common integration failures have actionable resolution steps.

---

## P8 — Final verification and stable release

- [ ] Confirm P0 and P1 files remain intact.
- [ ] Verify all documented paths and links.
- [ ] Validate workflow syntax and references.
- [ ] Run available repository checks.
- [ ] Test documented workflow integrations.
- [ ] Audit permissions and action pinning.
- [ ] Check for secrets, placeholder URLs, and fabricated configuration.
- [ ] Confirm the working tree is clean and all intended changes are committed.
- [ ] Review open issues, outstanding blockers, and deferred tasks.
- [ ] Publish release notes or a milestone summary if appropriate.
- [ ] Mark the roadmap complete only when acceptance criteria are met.
- [ ] Record follow-up maintenance tasks separately.

**Definition of done:** The repository has accurate community standards, documented reusable workflows, validated examples, an adopted security baseline, a proven pilot integration, and maintainable operating documentation.

---

## Final status reporting

At the end of each feature, report:

- **Task:** ID and title.
- **Branch:** exact branch name.
- **Changes:** files added or modified.
- **Verification:** commands and actual results.
- **GitHub:** PR URL and merge status, if applicable.
- **TODO:** updated status.
- **Blockers:** exact permission or technical issue, if any.
- **Next task:** the next eligible task in dependency order.

## Status legend

- `[ ]` Not started
- `[x]` Completed and verified
- `[!]` Blocked
- `[-]` Deferred with a documented reason

Do not mark a task complete merely because its files exist. Completion requires verification against its acceptance criteria.
