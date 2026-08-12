# AIRIS Predictions Playground

> ⚠️ **Experimental** — design working repo, not production code.

This repository hosts design assets for **AIRIS Predictions**: design templates (shells), mockups, prototypes, and design specs.

## Preview

**GitHub Pages: <https://beyza-ozbek-appier.github.io/airis-predictions-playground/>**

The root `index.html` links to the prototypes, specs, and docs under `playground/`, `shells/`, and `design-spec/`. The site is redeployed automatically on every push to `main` by `.github/workflows/pages.yml` (usually live within a minute).

> **Note on visibility:** this repository is public.

## Repository Structure

```
.
├── index.html                      # Card-based homepage for GitHub Pages
├── shells/                         # Design templates (shells) used as the base for mockups & prototypes
├── playground/                     # Experimental / work-in-progress outputs
│   ├── predictions-listing-v1/     # Original prototype + spec
│   └── predictions-listing-v2/     # Statuses & recommendation-warnings iteration + exhaustive docs
└── design-spec/                    # Finalized design specs (moved here once completed)
```

| Folder | Purpose |
| --- | --- |
| `shells/` | Reusable design templates ("shells") that serve as starting points. |
| `playground/` | Test and exploratory outputs. Anything here is disposable and subject to change. Each prototype iteration gets its own subfolder once it accumulates more than one or two files (see naming convention below). |
| `design-spec/` | Completed, agreed-upon design specs. Files graduate here from `playground/`. |

## Current prototypes

Both iterations of the Predictions Listing prototype are kept side by side rather than one replacing the other — see `index.html` for a rendered comparison card for each.

### `playground/predictions-listing-v1/` — original

All files are single-file, offline-capable HTML pages built on AIRIS (DS3) design tokens.

| File | What it is |
| --- | --- |
| `airis-prediction-listing-b.html` | Predictions list + detail + create/edit wizard prototype (Version B: single-page create). Carries a **state switcher** — 23 named states. |
| `airis-prediction-listing-b-spec.html` | Design-spec companion: 3 user flows as live embedded screens, plus **24 audited error scenarios** against the Design-System-Guideline error-message rubric (13 of those scenarios drove real copy fixes back into the prototype). |

### `playground/predictions-listing-v2/` — statuses & recommendation warnings

Adds the newer Behavior-Predictions-DS3.0 feature set on the same prototype lineage: 5 prediction statuses on one shared badge component, plus centralized "Refresh recommended" / "Retraining recommended" warnings with strict precedence (retraining always wins). Also adds a full documentation layer this iteration didn't have before.

| File | What it is |
| --- | --- |
| `predictions-listing-v2.html` | The prototype. Carries a 20-state dev switcher. |
| `predictions-listing-v2-spec.html` | Skill-conformant interactive design spec — 3 representative flows as live embedded screens, plus 6 audited error scenarios. |
| `predictions-listing-v2-user-flows.html` | **Exhaustive** visual flow inventory — 25 flows across 6 categories, a cross-cutting states catalog, explicit exclusions, and a Figma-coverage audit table covering every node reviewed. Complements, does not replace, the spec above. |
| `predictions-listing-v2-prd.md` | Scope/out-of-scope, user stories, BDD acceptance criteria, documented conflicts + resolutions, non-functional-control status, open questions, known limitations, glossary. |
| `predictions-listing-v2-readme.md` | What this iteration demonstrates, how to open/navigate it, known gaps. |
| `predictions-listing-v2-traceability.md` | Spec requirement → user-flow step → Figma node → HTML implementation → status. |
| `snapshots/` | Per-state PNG screenshots (headless Chrome), for quick viewing without opening the interactive spec. |

## Prototype states

Both prototypes ship a dev-only state switcher (the dark pill, bottom-right). Every state is a real
code path — `apply()` calls the page's own functions, never a faked overlay — so a state can't drift
from what the product code renders.

- Open a state directly: `<prototype>.html#state=<id>`
- Add `&shot` to hide the switcher pill for a clean capture: `#state=delete-2-confirm&shot`
- The state ids live in each prototype's own `window.PROTOSTATES` array near the bottom of the file.

## Design specs

Each `-spec.html` file is a **review & handoff document** — designers review it, engineers build from it. Its chrome is deliberately non-DS so it can never be mistaken for product UI. It embeds the live prototype per state (so the screens can never go stale) and carries an error-scenario table whose wording was audited against the
[error-message rubric](https://github.com/plaxieappier/Design-System-Guideline/blob/main/guidelines-ux-writing/error-message-rubric.md), recording `pass` or `fixed` per message with the before-text and rule IDs.

A prototype and its spec (and, for v2, its other docs) must stay in the **same directory** — the spec references the prototype by bare filename.

## Adding a New Prototype Iteration

1. Copy the relevant product shell from `shells/` into a new `playground/<slug>/` folder.
2. Build the prototype, then wire up `window.PROTOSTATES` for whatever states its flows need.
3. Only if a "design spec" / "handoff" is requested: copy `spec-template.html` from [Design-System-Guideline](https://github.com/plaxieappier/Design-System-Guideline) (`example-skills/ds-prototype-skill_local/resources/spec-template.html`) as `<slug>-spec.html`, fill in `SPECDATA`. Don't invent a different spec format.
4. If the work warrants it, add a PRD, README, traceability matrix, and/or an exhaustive user-flows document (see `predictions-listing-v2/` for the fullest example of this layer).
5. Generate snapshots if useful: `"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu --screenshot=snapshots/<state>.png --window-size=1440,900 "file://<slug>.html#state=<id>&shot"` for each state.
6. Add a card for it to `index.html`.

## File Naming Convention

Files inside `playground/` and `design-spec/` should be named flat within their own prototype folder, following:

```
<project-or-feature>-<version>
```

Examples already in this repo: `airis-prediction-listing-b.html` (v1, kept under its original name), `predictions-listing-v2.html` / `predictions-listing-v2-spec.html` (v2). A design spec is named after its prototype with a `-spec` suffix. Only create a prototype subfolder once a feature accumulates enough related files that flat naming at the `playground/` root becomes hard to scan — both iterations here qualify.
