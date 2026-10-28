# Permission and trust audit — 2026-10-28

Audit of every workflow in this repository. Status as of commit `b04cff0`
(main). Re-audit after any workflow change.

## Per-workflow permissions

| Workflow | Declared permissions | Trigger analysis |
|---|---|---|
| `reusable-ci.yml` | `contents: read` (workflow level) | Minimal; no secrets used; safe on any event |
| `reusable-security.yml` | `contents: read`, `security-events: write` (+ per-job `security-events: write` on CodeQL) | The `pull-requests: read` scope lives with the **caller** (validation contract, see below) |
| `reusable-release.yml` | `contents: write` | Justified: creates tags/releases. Runs on `workflow_dispatch` only — never on `pull_request`, so it cannot be triggered by untrusted code |
| `validation-actionlint.yml` | `contents: read` | Minimal; the lint binary is checksum-verified before execution |

## Third-party action pins

All 11 `uses:` references across the four workflows pin full commit SHAs,
resolved 2026-10-28 via `git ls-remote` (checkout ×4, setup-bun, pnpm
action-setup, setup-node, trufflehog, dependency-review-action, git-cliff,
codeql-action ×3). Dependabot keeps the pins current (weekly, grouped).

## Trust-boundary review

- **No `pull_request_target` anywhere.** No workflow executes pull-request
  code with repository write permissions. The release workflow cannot run
  from a PR at all.
- **Untrusted PR code execution:** CI stages run build/test scripts from PR
  branches on ephemeral `ubuntu-latest` runners with `contents: read` only.
  No secrets are exposed to CI stages (none are referenced). Accepted risk,
  standard for public repos.
- **Secrets:** the security reusable accepts the caller's token implicitly
  (`GITHUB_TOKEN`, auto-scoped); no named secrets exist. Nothing to leak.
- **Workflow-to-workflow trust:** reusable workflows inherit the caller's
  permissions per GitHub's rules; the permission contract in
  `reusable-security.yml` documents exactly what cascades.
- **Artifact handling:** no artifacts are uploaded by any workflow — no
  artifact-poisoning surface.

## Documented residual risks

1. **`pull-requests` caller contract** — a caller that omits the grant gets
   `startup_failure`. Mitigation: contract documented in three places;
   tested caller ships in `examples/`; actionlint in CI.
2. **Trufflehog runs `latest` image tag** — the action pulls
   `ghcr.io/trufflesecurity/trufflehog:latest` at run time; image content is
   upstream's release CI. Mitigation: accepted (action itself is SHA-pinned);
   revisitable if org policy hardens.
3. **`sha_pinning_required=false` at org level** — intentional: SHAs are
   pinned in-file and Dependabot maintains them; an org-wide enforcement
   would add friction without improving the actual guarantee.

**Acceptance:** no high-risk workflow pattern remains without a documented
mitigation or explicit decision. ✓
