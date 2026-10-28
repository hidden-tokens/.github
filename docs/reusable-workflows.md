# Reusable workflows

The org ships three reusable workflows in this repository. Call them from any
org repository's own workflow with `uses: hidden-tokens/.github/.github/workflows/<name>.yml@main`.
Copy-paste callers live in [`examples/`](../examples/).

All third-party actions are pinned to full commit SHAs; every job sets a
`timeout-minutes` cap. Unknown inputs are rejected at call time.

## Security — `reusable-security.yml`

Secret scan (Trufflehog) on every push (full history) and on PRs
(PR-diff mode); dependency review and CodeQL are PR-only.

| Input | Type | Default | Meaning |
|---|---|---|---|
| `runs-on` | string | `ubuntu-latest` | Runner label for all jobs |
| `codeql-languages` | string | `""` (off) | Comma-separated CodeQL languages, e.g. `typescript,javascript`. Empty skips CodeQL. Requires GHAS on private repos |
| `codeql-queries` | string | `security-extended` | Additional CodeQL query set |
| `dependency-review` | string | `auto` | `auto`: public repos only · `on`: always (needs GHAS on private) · `off`: never |

Permissions contract:

```yaml
# The reusable declares:      contents: read, security-events: write
# The CALLER must grant:      pull-requests: read          (required — see below)
# The CALLER may also grant:  security-events: write       (only if CodeQL enabled)
```

> **Why callers grant `pull-requests`:** a `workflow_call`-only reusable
> workflow must NOT declare `pull-requests` in its own permissions block —
> GitHub rejects the whole workflow at validation with an undiagnosable
> `startup_failure`. The caller's grant cascades into the called workflow.
> (Found at the core pilot, 2026-10-28, via a 7-run bisect.)

Free-tier facts: secret scanning works on every repo (public and private).
Dependency review needs GHAS on private repos, hence the `auto` default.
CodeQL is free on public repos only.

## CI — `reusable-ci.yml`

Staged lint → typecheck → test → build pipeline with per-stage toggles.
Every stage skips itself cleanly when the underlying files don't exist.

| Input | Type | Default | Meaning |
|---|---|---|---|
| `runs-on` | string | `ubuntu-latest` | Runner label |
| `working-directory` | string | `.` | Repo-relative working directory (monorepos: package path) |
| `package-manager` | string | `bun` | `bun`, `pnpm`, or `npm` (install/run commands derived) |
| `node-version` | string | `22` | Node version passed to the setup step |
| `lint` | boolean | `true` | Run lint check |
| `typecheck` | boolean | `true` | Run type check |
| `test` | boolean | `true` | Run tests |
| `build` | boolean | `true` | Run build |
| `audit` | boolean | `false` | Fail on known dependency vulnerabilities |

No secrets required for standard usage.

## Release — `reusable-release.yml`

Tag-and-publish pipeline driven by `workflow_dispatch` from the consuming
repo. **Always preview with `dry-run: true` first.**

| Input | Type | Default | Meaning |
|---|---|---|---|
| `tag` | string | — | Tag to release, e.g. `v1.2.3` |
| `title` | string | `""` | Release title (defaults to the tag) |
| `changelog` | boolean | `false` | Read/generate a changelog for the release body |
| `dry-run` | boolean | `false` | Preview only — no tag or release is published |
| `generate-notes` | boolean | `true` | Let GitHub generate release notes |
| `prerelease` | boolean | `false` | Mark the release as pre-release |
| `draft` | boolean | `false` | Create the release as a draft |
| `runs-on` | string | `ubuntu-latest` | Runner label |

Callers need `contents: write` (tag + release creation).

## Versioning and rollback

- `@main` is the stable reference; every merge to `main` is a reviewed cut
  from `develop` (feature branch → `develop` → `main`).
- To freeze against drift, pin a full commit SHA of this repo instead of
  `@main` — the pilot verified SHA-pinned calls work identically.
- Rollback = re-point the caller at the previous pin or at an earlier commit.
