# Hidden Tokens — GitHub Platform Roadmap

**Project:** `hidden-tokens/.github`
**Objective:** Build a maintainable, secure, reusable GitHub foundation for all Hidden Tokens repositories.
**Execution model:** One feature branch → implementation → verification → pull request → merge → next feature.

> **Amendment (2026-10-28, maintainer decision):** Branching follows `feature → develop → main`
> (develop is the integration branch; releases are cut via a `develop → main` PR), deviating from
> the "PR against main" baseline below. P0/P1 landed on `develop`; the first `develop → main` cut
> is pending release conventions (P4).

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
Pilot preselected: `hidden-tokens/bttf` (after the P2.2/P2.3 retrofit).

### P2.2 — Reusable CI workflow

Branch: `feature/reusable-ci` (actual: `feature/p2-workflows`, merged to `develop` 2026‑10‑28)

- [x] Add `.github/workflows/reusable-ci.yml`.
- [x] Define `workflow_call` inputs and required secrets only where necessary.
- [x] Support configurable checkout, runtime setup, dependency installation, linting, testing, and building where justified.
- [x] Avoid assuming every project uses the same language or package manager.
- [x] Set explicit, minimum workflow permissions.
- [ ] Pin third-party actions to verified full commit SHAs.
- [ ] Add timeouts and sensible failure behavior.
- [x] Document how callers pass inputs and consume results.
- [x] Validate workflow syntax (PyYAML) and reusable-workflow semantics.

**Acceptance criteria: NOT MET —** a minimal test repository or fixture invoking the workflow successfully is still pending.

### P2.3 — Reusable security workflow

Branch: `feature/reusable-security` (actual: same branch as P2.2, merged to `develop` 2026‑10‑28)

- [x] Add `.github/workflows/reusable-security.yml`.
- [x] Evaluate dependency review for pull requests.  (actions/dependency-review-action, high severity)
- [x] Evaluate supported static analysis and dependency scanning options.  (Trufflehog secret scan; CodeQL optional, off by default)
- [x] Make language-specific checks configurable.  (codeql-languages input)
- [x] Use least-privilege permissions and avoid unnecessary secrets.
- [ ] Pin third-party actions to full commit SHAs.
- [x] Document required GitHub features, permissions, and billing constraints.  (header notes free-tier vs GHAS)
- [ ] Test successful and failing scenarios where practical.

**Acceptance criteria: NOT MET —** documented on paper only; no run evidence yet.

### P2.4 — Caller documentation and examples

- [ ] Document the supported reusable workflows.
- [ ] Add minimal caller examples under a documented examples directory.
- [ ] Explain required permissions, inputs, secrets, and branch protections.
- [ ] Explain how repositories opt in and how to debug failed calls.
- [ ] Clearly distinguish tested examples from illustrative examples.

**Acceptance criteria:** A repository maintainer can integrate CI and security workflows by following the documentation alone.

---

## P3 — Automated quality and repository hygiene

### P3.1 — Workflow validation

- [ ] Add automated validation for workflow YAML and related configuration.
- [ ] Select a suitable action/workflow linter based on the repository’s actual needs.
- [ ] Validate reusable workflow interfaces and example callers.
- [ ] Add checks for accidental secret exposure and common configuration mistakes where practical.
- [ ] Keep validation fast enough for routine pull requests.

**Acceptance criteria:** Invalid workflow configuration is detected before merge.

### P3.2 — Dependency maintenance

- [ ] Evaluate Dependabot configuration for the repository’s actual dependencies.
- [ ] Configure GitHub Actions dependency updates where relevant.
- [ ] Set sensible update schedules and grouping to avoid excessive PR noise.
- [ ] Document how dependency update PRs are reviewed.
- [ ] Avoid enabling ecosystems that the repository does not use.

**Acceptance criteria:** Relevant dependencies have a maintainable update strategy without unnecessary automation.

### P3.3 — Code ownership and review conventions

- [ ] Decide whether a root `CODEOWNERS` file is appropriate.
- [ ] Define ownership only for real maintainers or teams.
- [ ] Document review expectations and approval responsibilities.
- [ ] Do not invent GitHub usernames, teams, or reviewer identities.
- [ ] Align ownership rules with actual repository permissions.

**Acceptance criteria:** Ownership rules, if enabled, reference valid maintainers and have a clear purpose.

### P3.4 — Standard repository metadata

- [ ] Audit licensing, funding, citation, and support metadata requirements.
- [ ] Add only applicable files, such as `FUNDING.yml`, `SUPPORT.md`, or `CITATION.cff`.
- [ ] Do not impose a license on repositories without an explicit licensing decision.
- [ ] Document which files are organization defaults and which must remain project-specific.

**Acceptance criteria:** Repository metadata is accurate, consistent, and does not make unauthorized legal or policy decisions.

---

## P4 — Release and change management

### P4.1 — Release conventions

- [ ] Define versioning and release expectations for shared workflows.
- [ ] Document how consumers pin reusable workflows to a release tag or commit SHA.
- [ ] Explain compatibility and breaking-change expectations.
- [ ] Define a changelog or release-note policy appropriate to the repository.
- [ ] Avoid adding a release automation system unless it materially improves maintenance.  (Note: `reusable-release.yml` already drafted ahead of schedule on `develop`; this task reviews whether to keep or replace it.)

**Acceptance criteria:** Consumers can identify stable versions and assess changes before upgrading.

### P4.2 — Reusable workflow compatibility

- [ ] Define a stable reference strategy for reusable workflows.
- [ ] Document immutable SHA references versus maintained version tags.
- [ ] Establish a process for publishing compatible updates.
- [ ] Define how breaking changes are announced.
- [ ] Verify examples use the documented reference strategy.

**Acceptance criteria:** Consumers can deliberately choose and update workflow versions.

---

## P5 — Organization adoption

### P5.1 — Integration guide

- [ ] Write a step-by-step guide for adopting the organization standards.
- [ ] Explain which files GitHub automatically discovers from the special `.github` repository and which require explicit integration.
- [ ] Include reusable workflow caller examples.
- [ ] Explain repository-specific overrides and limitations.
- [ ] Provide a checklist for maintainers adopting the standards.

**Acceptance criteria:** A maintainer can distinguish automatic organization defaults from features that must be configured in each repository.

### P5.2 — Pilot repository

- [ ] Select one suitable Hidden Tokens repository.
- [ ] Audit its existing CI before changing anything.
- [ ] Integrate the reusable CI workflow.
- [ ] Integrate security checks where supported.
- [ ] Verify pull request checks and failure reporting.
- [ ] Record compatibility issues and improve the shared workflows.
- [ ] Avoid broad rollout until the pilot succeeds.

**Acceptance criteria:** At least one real repository consumes the shared workflows successfully, with evidence from actual CI runs.

### P5.3 — Gradual rollout

- [ ] Inventory repositories eligible for adoption.
- [ ] Prioritize by activity, risk, and technical compatibility.
- [ ] Track adoption status and blockers.
- [ ] Document migration steps and rollback instructions.
- [ ] Roll out incrementally and verify each integration.

**Acceptance criteria:** Adoption is traceable and reversible; repositories are not changed blindly in bulk.

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
