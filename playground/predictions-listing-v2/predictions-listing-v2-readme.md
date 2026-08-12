# Predictions Listing v2 — prototype README

## What this demonstrates

The Predictions listing and detail pages for AIRIS, focused on two things: (1) a unified 5-status badge (Ready / Training / Refreshing / Failed / Archived), and (2) centralized "Refresh recommended" / "Retraining recommended" warnings on Ready predictions, shown as a badge on the list and a persistent banner on the detail page, with strict precedence (retraining always wins when both conditions are true).

## Files in this folder

| File | What it is |
|---|---|
| `predictions-listing-v2.html` | The prototype. Open directly in a browser. |
| `predictions-listing-v2-spec.html` | Interactive design spec (3 representative flows + 6 reviewed error/copy scenarios), in the format required by the Design-System-Guideline spec skill. |
| `predictions-listing-v2-user-flows.html` | Exhaustive flow inventory — every flow/subflow/state found in the relevant Figma scope, including everything the 3-flow spec above doesn't cover. |
| `predictions-listing-v2-prd.md` | Scope, user stories, acceptance criteria, documented conflicts, open questions, known limitations. |
| `predictions-listing-v2-traceability.md` | Spec requirement → flow step → Figma node → HTML implementation → status. |
| `snapshots/` | Static PNG screenshots, one per named state. |

## How to open it

Just open `predictions-listing-v2.html` in a browser — no server, no build step. To jump straight to a specific state (e.g. for review), append `#state=<id>` to the URL, or use the floating "States" pill in the bottom-right corner (dev-only, not part of the product UI) to pick one from a menu. The full list of state ids is in the spec and the user-flows document.

## Source

Copied from `AIRIS-UI/prototypes/airis-prediction-listing-b.html` and adapted — see the PRD's "Changes made in this playground" section for the exact diff summary (an added empty state, two widened table columns, and the `PROTOSTATES`/state-switcher retrofit needed for the spec to work). No other business logic was changed. The original file in `AIRIS-UI` is untouched.

## Known gaps (see the PRD for full detail)

- Search, the 4 filter pills, Export report, sorting, and pagination are all presentational only in this prototype — not part of this feature's scope, not silently hidden either. See PRD §6.
- Confirming Retrain/Archive/Unarchive/Delete closes the dialog only; no status mutation or async simulation exists for any of them.
- The Unarchive dialog's copy is this prototype's own (Figma's only Unarchive frame is internally self-contradictory) — labeled inferred throughout.
