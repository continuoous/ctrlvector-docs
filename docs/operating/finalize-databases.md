# Finalize databases

!!! warning "Page status: Draft"
    This chapter reflects the current finalization and totals-verification sequence. It requires product-owner and learner-clarity review before validation.

## Purpose

Finalization turns approved contributions into an Input Database that can support downstream comparison. Totals verification records the control check performed against that finalized result.

## Learning objectives

After reading this chapter, you should be able to:

- identify when a Workspace is ready for finalization;
- distinguish contribution approval from database finalization;
- distinguish finalization from totals verification;
- verify that finalized data is ready for Bridge creation.

## Prerequisites

Before opening `SUBFN`, confirm:

- the Submission Workspace is the intended Analysis, Version, and Period context;
- all contributions intended for inclusion have been submitted;
- the required contributions are approved in `SUBAP`;
- any rejected or missing contribution has an understood treatment.

## Finalize with SUBFN

1. Open `SUBFN`.
2. Select the intended Submission Workspace.
3. Review the approved contribution coverage.
4. Select the eligible contribution scope for finalization.
5. Finalize the Input Database.
6. Confirm that the finalized result is associated with the intended Workspace.

### Expected result

A finalized Input Database exists and becomes eligible for totals verification and downstream comparison.

## Verify totals with SUBTV

1. Open `SUBTV`.
2. Select the finalized Input Database.
3. Review the calculated totals in the intended business scope.
4. Compare them with the expected control totals.
5. Resolve differences before recording a successful verification.
6. Record the totals-verification result.

`SUBTV` depends on `SUBFN`; totals cannot be verified against a database that has not been finalized.

## Worked example

The monthly Actual Workspace contains approved Poland and Germany contributions.

1. Confirm both contributions are approved.
2. Finalize the eligible scope in `SUBFN`.
3. Open the finalized Input Database in `SUBTV`.
4. Compare its revenue total with the approved regional control total.
5. Record verification only when the totals agree.

### Expected result

The finalized Input Database has a recorded totals-verification result and can support a governed Bridge comparison.

## Verification checklist

- The correct Workspace was selected.
- Every included contribution is approved.
- Excluded or rejected contributions are understood.
- The finalized Input Database exists.
- The total was compared at the intended business scope.
- The verification result was recorded.
- The Version and Period match the intended downstream comparison.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| Finalization is unavailable | One or more required contributions are not approved. | Complete the review in `SUBAP`. |
| An expected contribution is absent | It was uploaded to another scope or was not submitted. | Return to `SUBST` and inspect the Workspace and Sub-Workspace selection. |
| Totals verification is unavailable | No finalized Input Database exists. | Complete `SUBFN`. |
| Control totals do not agree | The contribution scope, Version, Period, or source data differs from expectation. | Reconcile the context and contributions before recording verification. |

## Practice

For a Workspace with three contributors, document:

1. which contributions are eligible for finalization;
2. which control total will be used;
3. who reviews the difference;
4. what must happen before verification is recorded.

## Knowledge check

1. What does `SUBFN` create?
2. Can an unapproved contribution be finalized?
3. What is the difference between `SUBFN` and `SUBTV`?
4. Which finalized attributes should be checked before Bridge creation?

??? example "Answers"
    1. A finalized Input Database.
    2. No. Approval is the prerequisite.
    3. `SUBFN` finalizes approved input; `SUBTV` records totals verification against the finalized result.
    4. At minimum, its Analysis context, Version, Period, contribution coverage, and verified totals.

## Related material

- [Prepare and submit data](prepare-and-submit-data.md)
- [Create bridges](create-bridges.md)
- [End-to-end workflow](end-to-end-workflow.md)
