# Hidden Tokens — Compatibility Matrix (P2.1 audit)

**Date:** 2026-10-28 · **Task:** P2.1 Repository audit · **TODO:** see `TODO.md`

## Ownership-scope finding (affects everything downstream)

Org **defaults** in `hidden-tokens/.github` (issue templates, community files) auto-apply
**only to repositories inside the `hidden-tokens` org** — which currently holds just 2 repos
(`.github` itself and the empty `core`). The active product surface lives outside the org:

| Owner | Repos | Notes |
|---|---|---|
| `hidden-tokens` (org) | 2 | `.github` (this repo), `core` (private, empty) |
| `moughamir` (member, user-scope) | 57 non-fork, 12 pushed since Aug 2026 | Most product repos |
| `zawia-tech` (member's second org) | 6 | `wafine`, `sout`, `zawia-jeu`, … |

**Consequence:** user-scope repositories do **not** inherit org community files, and reusable
workflows are not auto-discovered anywhere — every repo must call them explicitly. If the
standards matter for the whole product surface, repos should eventually be **transferred into
the `hidden-tokens` org** (an org-owner decision, out of scope here) or the per-repo caller
pattern covers it.

## Active-repository matrix (pushed ≥ 2026-08-01)

| Repository | Owner | Language | PM / lockfile | Existing workflows | Shared-workflow fit |
|---|---|---|---|---|---|
| atre-vfs | moughamir | TypeScript | bun.lock | none | **CI candidate** |
| bttf | moughamir | TypeScript | bun.lock | none | **CI candidate** |
| buildmaroc-chantik-backend | moughamir | TypeScript | bun.lock | none | **CI candidate** |
| buildmaroc-chantik-backoffice | moughamir | TypeScript | bun.lock | none | **CI candidate** |
| dex | moughamir | TypeScript | bun.lock | `ci.yml`, `release.yml` | keep own, avoid duplication |
| dkv | moughamir | Rust | Cargo.toml | none | out of CI scope (no Rust reusable yet) |
| gameloop | moughamir | TypeScript | none in root | none | audit before wiring |
| moughamir.github.io | moughamir | Astro | bun.lock | `deploy.yml` | **CI candidate** (keep deploy) |
| moughamir | moughamir | — | n/a (profile) | none | n/a |
| openwa-pgmq | moughamir | — | n/a | none | n/a (placeholder) |
| owa | moughamir | TypeScript | package-lock + .nvmrc (npm) | 9 incl. `security-scan.yml` | keep own; model for release conventions |
| turi-plugin | moughamir | Rust/TS | pnpm-lock + Cargo.toml | 14 (covector, lint/test/audit) | keep own; richest pipeline in org |
| zawia-jeu | zawia-tech | JavaScript | package-lock (npm) | `tests.yml` | keep own or consolidate |
| sout | zawia-tech | Python | none in root | `tests.yml` | keep own; needs Python matrix if adopted |
| wafine | zawia-tech | TS/infra | none in root (Makefile, app/, terraform/) | 5 (app, docs, nightly-health, shell, upstream-watch) | infra-shaped; **security workflow fits** (secret scan/dpendency review), CI stage inputs mostly off |
| core | hidden-tokens | — | — | empty | n/a |
| .github (this) | hidden-tokens | docs | — | (reusable workflows only) | host |

Older/inactive repos (145 total non-fork under moughamir) are out of audit scope; engage per P5.3 inventory.

## Universal vs caller-configured checks

**Universal (no caller config):**

- Secret scanning — Trufflehog verified-only, works on any language/layout (already in `reusable-security.yml`).
- Dependency review on PRs — `actions/dependency-review-action`, works where a lockfile-driven PR diff exists (already in `reusable-security.yml`).

**Caller-configured:**

| Check | Applies | Caller input | Fallback when configured but absent |
|---|---|---|---|
| Lint | TS/JS + biome | default `lint: true` | skipped with notice (no script) |
| Typecheck | TS repos, `tsconfig.json` present | default `typecheck: true` | falls back to `tsc --noEmit`; skipped if neither |
| Test | script-driven | default `test: true` | skipped with notice |
| Build | script-driven | default `build: true` | skipped with notice |
| Audit | lockfile-dependent | opt-in `audit: true` | runs per-PM (`bun audit`/`pnpm audit`/`npm audit`) |
| CodeQL | public repos (free) / GHAS private | `codeql-languages` | empty input skips job |

**Language/PM coverage of convenience:**

- bun repos: install Bun regardless of Node setup — covered.
- npm/pnpm repos: covered.
- Rust (`dkv`, `turi-plugin`), Python (`sout`), infra (`wafine`, `core`): **no** `reusable` CI coverage — either security-only or keep own CI. A Rust/Python profile is a documented stretch goal, not P2.

**Duplication rule (contract):** repositories already running CI (dex, owa, turi-plugin, zawia-jeu, sout, wafine) keep their pipelines; only the security workflow may be layered where a pipeline lacks secret scanning (owa's own `security-scan.yml` already covers it — no addition).

## Pilot selection (for P5.2, corrected by maintainer 2026-10-28)

**Pilot: `hidden-tokens/core`** — the org's own shared open-core repository (maintainer scope:

- editor configs, dev-env bootstrapping, agents, 2nd-brain setups);

- first product/surface repo that lives *inside* the org, so the pilot proves what students

- need it to prove: org defaults + explicit workflow callers running on org reality.

~~Preselected: bttf~~ — struck: it lives under the `moughamir` personal account; Hidden

Tokens is strictly its own org, so user-scope repos are out of the pilot's proof value.

`bttf` remains a good *example* in the P2.4 caller docs, just not a pilot.

## Existing shared-workflow status (inputs to P2.2/P2.3 retrofit)

`reusable-ci.yml`, `reusable-security.yml`, `reusable-release.yml` exist on `develop`, but per
`TODO.md` their unmet acceptance items are: third-party actions pinned to full commit SHAs,
explicit per-job `timeout-minutes`, and one real fixture run (pilot candidate once retrofitted).
