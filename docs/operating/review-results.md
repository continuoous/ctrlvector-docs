# Review results

!!! warning "Page status: Draft"
    This chapter reflects the current BRRES task and requires product-owner and learner-clarity review before validation.

## Purpose

`BRRES` opens a persisted Bridge so a reviewer can confirm its context, inspect its results, narrow the scope with filters, and activate Factor analysis when deeper Factor-level review is needed.

## Learning objectives

After reading this chapter, you should be able to:

- select the intended persisted Bridge;
- confirm its Analysis, baseline, and current context;
- review the full Bridge before applying filters;
- narrow the result scope deliberately;
- distinguish initial result review from deeper interpretation.
- recognize a valid completed Bridge with no movement;
- trace a governed Factor explanation and action without mixing unrelated approval stories.

## Prerequisites

Before opening `BRRES`, confirm:

- the Bridge was created successfully with `BRDGE`;
- you know the intended Analysis and comparison Period;
- you know the business question the Bridge should answer;
- you understand which filters would change the review scope.

## Open and confirm the Bridge

1. Open `BRRES`.
2. Select the intended persisted Bridge.
3. Confirm the Bridge name or identifier.
4. Confirm the Analysis.
5. Confirm the baseline and current Version and Period.
6. Begin with the full Bridge before narrowing the scope.

If the context does not match the business question, stop and select the correct Bridge.

## Review and filter results

1. Review the overall movement for the full Bridge.
2. Inspect the available Node and Factor result organization.
3. Apply dimension filters only when the business question requires a narrower scope.
4. Select a Node when you need to focus on one calculation area.
5. Activate Factor analysis when Factor-level selection and review are needed.
6. Compare the filtered result with the full-Bridge context before drawing a conclusion.

Filters change the scope being reviewed; they do not create a new Bridge.

### Completed comparison with no movement

An unchanged Analysis may correctly have no impact rows. If BRRES shows **No movement detected**, verify that:

- baseline and current finalized totals are identical;
- change is zero;
- reconciliation difference is zero;
- the displayed Analysis and Version/Period lineage are correct.

Factor Analysis and per-unit controls are not applicable in this state. Do not rerun decomposition merely because no Factor rows exist.

### Governed story in Narrative Factor Detail

For a changed Factor, Narrative Factor Detail may include the approved explanation and follow-up action. Read the active analytical slice separately from the complete parent approval event. A selected member can be one part of an explanation that governed several Contributions; the card should preserve that complete scope while identifying the active match.

Confirm the reason, approver conclusion, mitigation, residual risk, owner, and due date. Only evidence matching the active Analysis, Node, Factor, and member filters should appear.

## Worked example

For the January-to-February Actual revenue Bridge:

1. Select the saved Bridge in `BRRES`.
2. Confirm January is the baseline and February is current.
3. Review the full revenue movement.
4. Apply the `Europe` Geography filter.
5. Select the relevant revenue Node.
6. Activate Factor analysis if a specific Factor requires closer inspection.
7. Record both the filtered conclusion and the scope used.

### Expected result

The reviewer can explain which persisted Bridge was inspected, which scope was active, and whether the conclusion applies to the full Bridge or only to a filtered subset.

## Verification checklist

- The selected Bridge is the intended result.
- The Analysis is correct.
- Baseline and current are in the intended order.
- Version and Period labels match the business question.
- The initial review used the full Bridge.
- Every applied filter is visible and intentional.
- Node or Factor focus is recorded with the conclusion.
- The result remains traceable to the persisted Bridge.
- A no-movement result, when present, shows equal totals and zero reconciliation difference.
- Governed narrative evidence matches the active Factor scope and preserves its parent event.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| No result is available | No completed Bridge has been selected or created. | Create or select a Bridge through `BRDGE`. |
| The movement direction is unexpected | Baseline and current may be reversed. | Recheck the Bridge context before interpretation. |
| Totals change after filtering | The active filter narrows the business scope. | Review the filter list and compare with the full Bridge. |
| A Factor cannot be selected | Factor analysis has not been activated. | Activate Factor analysis for the loaded Bridge. |
| A conclusion cannot be reproduced | The Bridge, filter, Node, or Factor scope was not recorded. | Capture the complete review context. |
| A completed unchanged Bridge appears empty | Zero impact rows were mistaken for missing decomposition data. | Confirm the explicit no-movement state, equal totals, comparison lineage, and zero reconciliation difference. |
| Narrative evidence appears unrelated | The approval story does not match the active analytical scope. | Recheck the Analysis, Node, Factor, and member filters; do not combine unrelated Workspace narratives. |

## Practice

Open one Bridge and write two review statements:

1. one for the full Bridge;
2. one for a filtered dimension scope.

For each statement, record the Bridge, Analysis, baseline, current, filters, and any Node or Factor focus.

## Knowledge check

1. Which transaction displays persisted Bridge results?
2. What should be checked before interpreting movement?
3. Do filters create a new Bridge?
4. When is Factor analysis needed?

??? example "Answers"
    1. `BRRES`.
    2. The Bridge identity, Analysis, baseline, current, Version, and Period context.
    3. No. They narrow the current review scope.
    4. When the reviewer needs Factor-level selection and analysis.

## Related material

- [Create bridges](create-bridges.md)
- [Read a bridge](../analysis/read-a-bridge.md)
- [Dimension drilldowns](../analysis/dimension-drilldowns.md)
- [Transaction-code reference](../reference/transaction-codes.md)
