# AIRIS Predictions Playground

> ⚠️ **Experimental** — design working repo, not production code.

This repository hosts design assets for **AIRIS Predictions**: design templates (shells), mockups, prototypes, and design specs.

## Preview

**GitHub Pages: <https://beyza-ozbek-appier.github.io/airis-predictions-playground/>**

The root `index.html` is a directory page linking to the HTML files under `shells/`, `playground/` and `design-spec/`. The site is redeployed automatically on every push to `main` by `.github/workflows/pages.yml` (usually live within a minute).

> **Note on visibility:** this repository is private, but the published Pages site is publicly reachable by anyone with the URL. Keep sensitive content out of this repo.

## Repository Structure

```
.
├── index.html     # Directory page for GitHub Pages preview
├── shells/        # Design templates (shells) used as the base for mockups & prototypes
├── playground/    # Experimental / work-in-progress outputs
└── design-spec/   # Finalized design specs (moved here once completed)
```

| Folder | Purpose |
| --- | --- |
| `shells/` | Reusable design templates ("shells") that serve as starting points. |
| `playground/` | Test and exploratory outputs. Anything here is disposable and subject to change. |
| `design-spec/` | Completed, agreed-upon design specs. Files graduate here from `playground/`. |

## Current Files

All files are single-file, offline-capable HTML pages built on AIRIS (DS3) design tokens.

| File | What it is |
| --- | --- |
| `shells/airis-shell.html` | Canonical AIRIS product chrome — the shell prototypes are built from. Copied from [Design-System-Guideline](https://github.com/plaxieappier/Design-System-Guideline). |
| `playground/airis-prediction-listing-b.html` | Predictions list + detail + create/edit wizard prototype (Version B: single-page create). Carries a **state switcher** — 23 named states, see below. |
| `playground/airis-prediction-listing-b-spec.html` | Design-spec companion to the prototype: 3 user flows as live embedded screens, plus 24 audited error scenarios. |

## Prototype states

The prototype ships a dev-only state switcher (the dark pill, bottom-right). Every state is a real
code path — `apply()` calls the page's own functions, never a faked overlay — so a state can't drift
from what the product code renders.

- Open a state directly: `airis-prediction-listing-b.html#state=<id>`
- Add `&shot` to hide the switcher pill for a clean capture: `#state=delete-2-confirm&shot`
- The state ids live in the `window.PROTOSTATES` array near the bottom of the prototype file.

## Design spec

`playground/airis-prediction-listing-b-spec.html` is a **review & handoff document** — designers
review it, engineers build from it. Its chrome is deliberately non-DS so it can never be mistaken
for product UI.

It embeds the live prototype per state (so the screens can never go stale) and carries an
error-scenario table whose wording was audited against the
[error-message rubric](https://github.com/plaxieappier/Design-System-Guideline/blob/main/guidelines-ux-writing/error-message-rubric.md).
The audit column records `pass` or `fixed` per message, with the before-text and the rule IDs that
forced each change.

Both files must stay in the **same directory** — the spec references the prototype by bare filename.

## Adding a New File

1. Drop the HTML file into `shells/` or `playground/`, following the naming convention below.
2. Add its filename to the `FILES` list near the bottom of `index.html` (one line per file).
3. Commit and push — GitHub Pages redeploys automatically.

## File Naming Convention

Files inside `playground/` and `design-spec/` should be named flat (no subfolders) whenever possible, following:

```
airis-<project-or-feature>-<version>
```

Examples:

- `airis-prediction-listing-b.html`
- `airis-prediction-detail-v2.html`
- `airis-prediction-listing-b-spec.html`

Guidelines:

- Use lowercase kebab-case.
- Bump the version suffix (`v1`, `v2`, …) instead of overwriting previous versions.
- A design spec is named after its prototype with a `-spec` suffix.
- Only create subfolders when a feature accumulates enough related files that flat naming becomes hard to scan.
