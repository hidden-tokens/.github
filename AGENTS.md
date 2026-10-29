# AGENTS.md — hidden-tokens/.github — instructions canoniques

Instructions pour tout agent travaillant dans ce dépôt (`hidden-tokens/.github`).
[`CLAUDE.md`](CLAUDE.md) et [`GEMINI.md`](GEMINI.md) sont des renvois vers ce fichier canonique : édite `AGENTS.md` seul.

Ce dépôt héberge les standards de l'organisation Hidden Tokens, ses templates de communauté et ses workflows réutilisables.

## Règles strictes (Git Flow)

- **Git flow** : `feature → develop → main`. Les pull requests se font exclusivement vers **`develop`** ; jamais de commit direct sur `main`, aucun force-push.
- **Préfixes de branches** : `feature/`, `chore/`, `docs/`, `fix/` ; commits conventionnels (`feat:`, `chore:`, `docs:`, `fix:`).
- **`TODO.md`** : c'est le registre de tâches avec le contrat d'exécution d'agent au début. Toujours vérifier que la branche et l'arbre de travail sont propres avant d'entamer une tâche.
- **Workflows GitHub** : les fichiers sous `.github/workflows/` sont validés par actionlint en CI. Les workflows réutilisables n'auto-s'appliquent pas : les dépôts appelants doivent les invoquer explicitement (voir `docs/`).
- **Règle de confidentialité** : aucun nom réel, identifiant, montant, document confidentiel ou token ne doit apparaître dans les issues, PRs ou le code.

<!-- graft:start -->
## Graft — repo context graph

This repo is indexed in `graft/`: small linked markdown nodes that explain each
system and carry exact file:line spans, kept in sync with the code through git.

For ANY task here — understanding how something works, finding where code lives,
or scoping a change — get context from the graph before grepping or opening
source files. Re-ask freely (it's cheap) and reuse literal identifiers you
already have (symbol, error string, file name) as the query. New to this repo?
Run `graft map` first — a token-budgeted orientation (dir clusters, hubs,
hotspots), no LLM, no key.

- Run `graft ask "<your question>" --source` → ranked nodes with the relevant
  code spans inlined (each hit's ≤8-line crux by default; `--full` for whole
  definitions when the crux isn't enough). Match the tool to the task shape:
  for understanding or editing, the top node IS the answer — cite its
  `covers:` file:line spans and edit straight from `--source`. For
  exhaustive tasks ("every occurrence / every caller of this pattern"), ranked
  results are top-N, not complete — run `graft grep "<literal>"` instead
  (exhaustive over indexed files, grouped by enclosing symbol), falling back
  to raw `grep -rn` only for unindexed files.
- `graft skeleton <file>` → every definition's signature + span, ~10× cheaper
  than reading the file; use it to skim an API surface.
- `graft callers <symbol>` gives precomputed, exact edges — who calls this.
  Add `--direction out` for what it calls, or `--depth N` to walk
  transitively for the full blast radius. For structural questions, skip
  ranking and use this directly.
- Or browse: `graft/INDEX.md` lists every node; follow the links.
- Monorepos and folders of multiple repos rank fairly across sub-projects —
  hits carry `[scope/]` labels naming which one they're from. Narrow with
  `graft ask "<task>" --in <scope>/` once you know where you're working.

If a returned span is truncated ("+N more lines"), open the file at that exact
range before finalizing. Only open source files when a node genuinely lacks a
needed detail, and then at the exact file:line the node points to — never
re-read whole files.

After big code changes, refresh the graph with `graft build` (deterministic,
no API key, $0).
<!-- graft:end -->
