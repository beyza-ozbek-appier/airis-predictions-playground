# PRD: Predictions Listing v2 — Statuses & Recommendation Warnings

> Companion documents: [`predictions-listing-v2-spec.html`](predictions-listing-v2-spec.html) (interactive design spec, 3 flows) · [`predictions-listing-v2-user-flows.html`](predictions-listing-v2-user-flows.html) (exhaustive flow inventory) · [`predictions-listing-v2-traceability.md`](predictions-listing-v2-traceability.md) · [`predictions-listing-v2-readme.md`](predictions-listing-v2-readme.md)

**Provenance**
- Prototype source: `AIRIS-UI/prototypes/airis-prediction-listing-b.html` (copied into this playground, then adapted — see "Changes made in this playground" below).
- Figma source of truth: [Behavior-Predictions-DS3.0](https://www.figma.com/design/8MTdgBEhjTTdSlU8ilrmlZ/Behavior-Predictions-DS3.0?node-id=354-30391), section `354:30391`.
- Design-system rules followed: `plaxieappier/Design-System-Guideline` @ commit `7e8cedc8` (2026-08-03).
- Spec format followed: `example-skills/ds-prototype-skill_local/resources/references/design-spec.md` + `resources/spec-template.html` (same commit). See that repo's `example-skills/README.md` for why this isn't vendored into this repo (authoring-time reference, not a runtime dependency).
- UX-writing rubric: `guidelines-ux-writing/error-message-rubric.md` (same commit).
- Generated: 2026-08-12.

---

## 1. Scope

This spec covers the Predictions Listing v2 prototype's **status model and recommendation-warning feature**:
- 5 prediction statuses (Ready, Training, Refreshing, Failed, Archived) rendered via one shared status-badge component.
- Two recommendation warnings ("Refresh recommended", "Retraining recommended") shown only on Ready predictions, with a centralized decision rule and strict precedence (retraining always wins).
- Where those warnings surface: a badge on the listing table (under the relevant date column) and a persistent banner on the prediction detail page.
- The row-level action menu (Refresh, Retrain, Activity log, Archive/Unarchive, Delete) and its disabled/guarded states, since the recommendation feature depends on and extends this menu.
- The empty/cold-start state (added in this playground — see §9).

**Out of scope for this spec** (see §5 for why):
- The Segments → Filter dropdown → Selected prediction compact warning indicator. Figma confirms this is real designed scope (Note `585:28378`c), but no Segments feature exists in this codebase to implement it against. Documented here, not built.
- Search, filtering, sorting, pagination, and export on the listing table — see §6, all presentational.
- Any change to the underlying create/edit prediction wizard beyond what's needed to demonstrate the entry point from the empty state.
- Max prediction count, health-score gating, and retrain credit thresholds — open PM questions per Figma notes, not decided anywhere. See §7.

## 2. User stories

- As a marketer with a Ready prediction, I want to see at a glance whether it needs refreshing or retraining, so I don't act on stale results.
- As a marketer, I want the system to tell me *which one* action matters most (retrain vs. refresh) when both could apply, so I don't have to guess or do redundant work.
- As a marketer, I want a disabled action (e.g. Retrain during Training) to tell me why, so I'm not left guessing whether it's broken.
- As a new AIRIS Predictions user with none created yet, I want a clear empty state that gets me straight into creating my first prediction.

## 3. Primary flow (interactive spec)

See `predictions-listing-v2-spec.html`, flow `list-lifecycle`. Summary: land on Predictions (populated, or empty on first use) → optionally create from empty state → open a prediction.

## 4. Acceptance criteria (BDD)

```gherkin
Feature: Recommendation warnings on Ready predictions

  Scenario: Refresh recommended only
    Given a prediction with status "Ready"
    And it has new source data since its last successful refresh
    And it has not accumulated a full prediction-window of new training data
    Then the listing shows "Refresh recommended" under Last Inference date
    And the detail page shows a persistent "Refresh recommended" banner
    And no "Retraining recommended" warning is shown

  Scenario: Retraining recommended wins when both are true
    Given a prediction with status "Ready"
    And it has new source data since its last successful refresh
    And it has accumulated at least one prediction-window of new training data since last training
    Then the listing shows "Retraining recommended" under Last train date
    And "Refresh recommended" is not shown, even though its condition is also true

  Scenario: No recommendation outside Ready
    Given a prediction with status "Training", "Refreshing", "Failed", or "Archived"
    And both recommendation conditions would otherwise be true
    Then neither recommendation badge nor banner is shown

  Scenario: Example scenario from Figma (Product Purchase, predictionWindow=7)
    Given last trained Aug 1 and last refreshed Aug 9
    When on Aug 10 one new source-data batch exists and training data has not reached 7 days
    Then only "Refresh recommended" is shown
    When on Aug 15 training data has reached 7 days (source data still newer too)
    Then only "Retraining recommended" is shown

  Scenario: Retrain blocked by status
    Given a prediction not in "Ready" status
    When the user opens the row menu
    Then "Retrain model" is disabled with tooltip "Only Ready models can be retrained."

  Scenario: Retrain blocked by cooldown
    Given a prediction in "Ready" status trained less than 7 days ago
    When the user opens the row menu
    Then "Retrain model" is disabled with a tooltip naming the last-retrained date and the 7-day rule

  Scenario: Archive/Delete blocked while busy
    Given a prediction in "Training" or "Refreshing" status
    When the user opens the row menu
    Then "Archive"/"Unarchive" and "Delete" are both disabled with a reason tooltip

  Scenario: Empty state
    Given no predictions exist
    Then the table, toolbar, and pagination are hidden
    And a message plus a secondary "Create prediction" action are shown instead
```

## 5. Documented conflicts and how they were resolved

| # | Conflict | Resolution | Source priority applied |
|---|---|---|---|
| 1 | Status badge radius: Figma ships `radius/r8-(m)` (8px, verified via `get_design_context` on node `354:30744`); `component-guidelines/status.md`/`DESIGN-DS3.md` document `radius-4`. | Kept 8px. The doc's radius table has no `r8-(m)` entry at all — read as the doc predating this component's current token, not Figma being wrong. Flagged for the design-system maintainers; not fixed in this playground. | Figma value is explicitly verified → takes priority per the agreed rule. |
| 2 | Figma's "Training" and "Refreshing" status descriptions are verbatim-identical ("Model training is currently in progress...") — almost certainly a copy-paste slip, since Refreshing should describe refreshing, not training. | Not propagated. Recorded here as a design-content issue; the prototype's status labels are unaffected (only the dot/tint/label are shown, not this description text). | N/A — treated as a content defect, not a real requirement. |
| 3 | Figma's only "Unarchive" dialog frame (`372:41253`) has a title that says "Unarchive?" but body/button text that says "Archive" — internally contradictory, not a usable spec. | Kept the prototype's own self-consistent Unarchive copy. Labeled **inferred, not Figma-confirmed** everywhere it's referenced (spec, traceability). | No verifiable Figma value exists → existing HTML, clearly labeled. |
| 4 | Two Figma Archive-dialog variants disagree on wording ("stop all scheduled inference runs" vs. "stop all scheduled refreshes"). | Kept the prototype's existing copy (matches the first variant). Cosmetic; not re-derived. | Existing HTML, since both Figma variants are equally "confirmed" and the choice is immaterial. |
| 5 | Segments recommendation indicator (Note `585:28378`c) is real Figma scope but no Segments feature exists here. | Documented as explicitly out of scope (§1), not built, not silently dropped. | Per your decision. |
| 6 | Max prediction count, health-score threshold, retrain credit threshold — Figma notes (`638:29220`, `354:30711`, `638:27503`) flag these as PM-undecided. | Left as open questions (§7). No answer invented. | Per your decision. |

## 6. Non-functional controls (existing prototype)

| Control | Figma/annotation confirms behavior? | Current behavior | Status |
|---|---|---|---|
| Search input | No | No event handler at all — fully inert | **Prototype limitation.** Out of scope for this feature; unrelated to statuses/recommendations. |
| Filter pills (Type/Tag/Source/Exports) | No | `onclick="return false;"` | **Prototype limitation.** Out of scope. |
| Export report | No | `onclick="return false;"` | **Prototype limitation.** Out of scope. |
| Pagination | Figma shows a `*Pagination` component structurally; no behavior annotation | First/Previous hardcoded disabled; Next/Last have no handler | **Prototype limitation.** Out of scope. |
| Sort (3 date columns) | No | Toggles arrow direction only; does not truly re-sort by date | **Prototype limitation.** Out of scope. |

None of these are required by this ticket's scope (status/recommendation model). They are called out here, in the README, and in the traceability matrix so they are never silently non-functional.

## 7. Open questions (do not answer here — PM/design decision needed)

1. Maximum number of predictions per account — Figma Note `638:29220`, "PM need to approve maximum number for prediction," undefined.
2. Health-score gating on model usage after training — Figma Note `354:30711`: the PRD it references "doesn't define how the health score is calculated, what the minimum threshold is, or which metrics it is based on."
3. Minimum retrain threshold tied to credit consumption — Figma Note `638:27503`, "PM should define the min threshold for retrain," undefined.
4. Figma Note `354:30707` ("PM Discussions") contains an unfinished line — "NEED RETRAINING →" with no content — possibly an abandoned earlier status-naming direction, superseded by the recommendation-badge approach. Not acted on.
5. What does a real Retrain/Refresh/Archive/Delete failure look like end-to-end? Figma Note `354:30713` confirms a failed retrain moves the model to "Failed" status, but no UI copy or flow exists for it beyond that one (typo'd) note.

## 8. Known limitations (this prototype)

- Confirming Retrain, Archive, Unarchive, or Delete in their dialogs closes the dialog only — no status mutation, no toast, no simulated async job. This is pre-existing prototype behavior, not something introduced or fixed here.
- No error/failure state is simulated for any action (see Open Question 5).
- The Refresh row-menu submenu is labeled "Edit refresh schedule" / "Refresh now" in the prototype, vs. Figma's confirmed "Schedule refresh" / "Refresh now" (node `372:39811`). Same intent, different label; not changed here since it predates this ticket.
- Table columns are ordered Name/Status/Prediction type/**Last train date/Last Inference date**/Created; Figma's Note `354:30704` lists **Last refreshed** before **Last trained**, and calls the refreshed-date column "Last refreshed" rather than "Last Inference date." Kept as-is to avoid an unrelated table-shape change.
- Two table columns (`Last train date`, `Last Inference date`) were widened in this playground copy only (143px→190px, 168px→200px) because the "Retraining recommended"/"Refresh recommended" badge text clipped at the original widths — confirmed by rendering the prototype in headless Chrome during Phase 2 validation. This is a visual bug fix to the playground's copy, not applied back to the source AIRIS-UI file.

## 9. Changes made in this playground (vs. the copied source file)

1. Added the confirmed-but-missing empty/cold-start state (Figma `354:31700`): `setPredictionsListEmpty()` + `#emptyStatePanel` markup.
2. Widened two table columns to stop recommendation-badge text clipping (see §8).
3. Added `window.PROTOSTATES` (18 states) and the official state-switcher widget (copied verbatim from Design-System-Guideline's `state-switcher.html`) so the design spec's flow thumbnails and this PRD's traceability matrix can point at real, reproducible states.
4. No other business logic was changed.

## 10. Glossary

- **Ready** — model trained and up to date; the only status recommendations can appear on.
- **Refresh** — re-run inference with the current model on latest data (fast).
- **Retrain** — train a new model version on latest data (slow, 1–2 hours per Figma copy); triggers an automatic refresh afterward.
- **Recommendation** — a non-blocking warning suggesting Refresh or Retrain; never replaces or overrides the status.
- **Prediction window** — the number of days the prediction predicts over; also the threshold (in days of new completed training data) that triggers "Retraining recommended."
