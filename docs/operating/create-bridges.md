# Create bridges

!!! warning "Page status: Draft"
    This chapter reflects the current BRDGE task and requires product-owner and learner-clarity review before validation.

## Purpose

A Bridge compares eligible finalized business data in the context of an Analysis. `BRDGE` creates and persists that governed comparison so it can be reviewed consistently in `BRRES`.

## Learning objectives

After reading this chapter, you should be able to:

- identify the required Bridge inputs;
- select the intended finalized comparison and Analysis;
- create a persisted Bridge;
- verify that the Bridge is ready for result review.

## Prerequisites

Before opening `BRDGE`, confirm:

- the required Input Databases have been finalized through `SUBFN`;
- their business context represents the intended comparison;
- the intended Analysis Definition exists;
- totals and contribution coverage have been reviewed;
- the baseline and current meanings are understood.

`RUNCR` and `RUNSB` are not prerequisites for this procedure. Their current task pages are unavailable, and an official Run is not a hard prerequisite for Bridge creation.

## Create the Bridge

1. Open `BRDGE`.
2. Select the eligible finalized comparison and Analysis.
3. Confirm the baseline and current Version and Period context.
4. Add a concise business note when it helps reviewers understand the comparison.
5. Create the Bridge.
6. Wait for calculation and persistence to complete.
7. Record the Bridge identity for review.

### Expected result

CtrlVector creates a persisted Bridge result that can be opened in `BRRES`.

## Worked example

Create a Bridge comparing January Actual revenue with February Actual revenue:

1. Confirm both monthly Input Databases are finalized.
2. Confirm the revenue Analysis is the intended analytical scope.
3. Select the January-to-February eligible comparison.
4. Add a business note such as `Monthly Actual revenue review`.
5. Create the Bridge.
6. Open `BRRES` and select the new result.

### Expected result

The Bridge is available with the intended Analysis, baseline context, and current context.

## Verification checklist

- Both sides of the comparison are eligible finalized data.
- The baseline and current order is intentional.
- Version and Period labels match the business question.
- The selected Analysis is correct.
- Bridge creation completes successfully.
- The Bridge appears in `BRRES`.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| No eligible comparison is available | Required finalized data or comparison context is missing. | Verify the Input Databases and Analysis. |
| Baseline and current appear reversed | The comparison was selected in the wrong direction. | Recheck the business question before creating the Bridge. |
| The intended Analysis is absent | The Analysis Definition is incomplete or not eligible for the comparison. | Verify it in `ANA03`. |
| A Run transaction cannot be opened | `RUNCR` and `RUNSB` have no current task pages. | Continue only with the documented available Bridge procedure. |

## Practice

Write a Bridge request that states:

1. the business question;
2. the baseline Version and Period;
3. the current Version and Period;
4. the Analysis;
5. the expected reviewer.

## Knowledge check

1. Which transaction creates a Bridge?
2. What two broad inputs does Bridge creation require?
3. Is `RUNSB` a hard prerequisite for `BRDGE`?
4. Which transaction opens the persisted result?

??? example "Answers"
    1. `BRDGE`.
    2. Eligible finalized comparison data and an Analysis.
    3. No.
    4. `BRRES`.

## Related material

- [Finalize databases](finalize-databases.md)
- [Review results](review-results.md)
- [Read a bridge](../analysis/read-a-bridge.md)
- [Transaction-code reference](../reference/transaction-codes.md)
