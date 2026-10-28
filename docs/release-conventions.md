# Release conventions

How `hidden-tokens/.github` (the platform repo) and its consumers handle
versions, references, and breaking changes.

## The platform repo's release flow

- All work lands on feature branches → PR → `develop`.
- A **cut** is a reviewed PR `develop` → `main`; every merge to `main` is a
  release candidate. History shows the cut cadence in the merge trail.
- **After every cut, fast-forward `develop` to `main`** (`git push origin
  origin/main:develop`). Cut merge commits land only on `main`, so without
  this step `develop` accumulates a "behind main" count of one merge commit
  per cut. Fast-forwarding keeps both branches identical between cuts.
- Tags (`vX.Y.Z`) are cut from `main` when a stable milestone is reached —
  not per merge. Tags are immutable once published.
- Release notes are GitHub-generated from merged PRs (no hand-maintained
  CHANGELOG in this repo — the merge trail is the changelog).

## Versioning semantics for reusable workflows

Changes to the reusable workflows follow semver-flavored intent:

| Change | Consequence for consumers |
|---|---|
| New optional input, new job, docs | **Patch/minor** — consumers notice nothing |
| Behavior change behind a default-off input | **Minor** |
| Default value change, stage reordering, output rename | **Minor, flagged** in release notes |
| Input removal/rename, stage removal, hard requirement added | **Major** — breaking; announced, see below |

## Reference strategy for consumers (P4.2)

Three valid references, in order of preference:

1. **`@main`** — default. Always current, reviewed by the cut PR. Right for
   org-internal repos that want the latest fixes immediately (the pilot uses
   this).
2. **Full commit SHA** — change-controlled. Nothing moves until you re-pin.
   The pilot verified SHA-pinned calls work identically. Resolve with
   `git ls-remote https://github.com/hidden-tokens/.github.git main`.
3. **`@vX.Y.Z` tag** — stable snapshots for cautious consumers, available
   once a milestone is tagged. Branch names other than `main` are not
   supported references.

Do **not** reference `@develop` — it is an integration branch, not a release
surface.

## Breaking-change process

1. Breaking change merges to `develop` with `BREAKING:` in the PR title.
2. The cut PR to `main` lists the breakage at the top of its body and in the
   release notes.
3. Examples in this repo are updated in the same PR that breaks them —
   examples always demonstrate the current contract.

## Release automation

`reusable-release.yml` (git-cliff changelog + tag guard + GitHub Release) is
**kept**: product repos need reproducible tagged releases, and the workflow
already exists, is SHA-pinned, and exposes a mandatory `dry-run` preview path.
It is illustrative until its first real run (see `examples/README.md`).
