# Finalize databases

!!! warning "Page status: Draft"
    This chapter reflects the current finalization and totals-verification sequence. It requires product-owner and learner-clarity review before validation.

## Purpose

Finalization turns an approved Workspace decision into Analysis Input Databases that can support downstream comparison. Totals verification records the control check performed against each finalized result.

## Learning objectives

After reading this chapter, you should be able to:

- identify when a Workspace is ready for finalization;
- distinguish contribution approval from database finalization;
- understand how one approved Workspace produces several Analysis artifacts;
- distinguish finalization from totals verification;
- verify that finalized data is ready for Bridge creation.

## Prerequisites

Before opening `SUBFN`, confirm:

- the Submission Workspace is the intended Analysis, Version, and Period context;
- all contributions intended for inclusion have been submitted;
- the current Workspace candidate is approved in `SUBAP`;
- any rejected or missing contribution has an understood treatment.

## Finalize with SUBFN

1. Open `SUBFN`.
2. Select the intended Submission Workspace.
3. Review the approved contribution coverage.
4. Review every eligible Analysis Sub-Workspace.
5. Finalize the approved Analysis artifacts together.
6. Confirm that one immutable Input Database exists for each required Analysis.
7. Confirm that every artifact traces to the same approved Workspace candidate.

### Expected result

Every required Analysis has its own finalized Input Database and becomes eligible for totals verification and downstream comparison. The parent Workspace is finalized only after the required Analysis artifacts exist.

### Technical retry

If finalization is repeated after an interruption, completed Analysis artifacts should be reused or skipped. The retry must not create duplicates, reopen Contributions, or request another business approval.

### Approval and narrative lineage

Finalized artifacts retain the business-decision context authorized in `SUBAP`, including scoped explanations, policy outcomes, accepted residual items, mitigations, action owners, and due dates. This lineage supports later audit and story-behind-the-numbers review without changing the immutable submitted facts.

## Verify totals with SUBTV

1. Open `SUBTV`.
2. Select the finalized Workspace.
3. Use **All Sub-Workspaces** when the Workspace contains several Analyses.
4. Review every Node beneath its Analysis.
5. Compare each calculated finalized total with its approved expected total.
6. Resolve differences before recording a successful verification.
7. Record the Analysis verification statements.
8. Confirm that the parent Workspace reports totals verified only after every required Analysis passes.

`SUBTV` depends on `SUBFN`; totals cannot be verified against a database that has not been finalized.

Do not add Nodes with unlike formats. A financial statement and an operational statement may be reviewed together on one page while retaining separate units and verification outcomes.

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
- One artifact exists for each required Analysis.
- Every artifact belongs to the same approved Workspace decision.
- A repeated finalization request creates no duplicate artifact.
- Approval narratives and accountable actions remain traceable.
- The total was compared at the intended business scope.
- Every governed Node was reviewed beneath the correct Analysis.
- Unlike units were not aggregated.
- The parent Workspace verification state agrees with the Analysis statements.
- The verification result was recorded.
- The Version and Period match the intended downstream comparison.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| Finalization is unavailable | The Workspace is incomplete, stale, or not approved. | Complete submission coverage, recalculate if needed, and decide the Workspace in `SUBAP`. |
| An expected contribution is absent | It was uploaded to another scope or was not submitted. | Return to `SST01`, `SST02`, or `SST09` and inspect the Workspace, Factor, and Contribution scope. |
| Only some Analysis artifacts exist | A technical failure interrupted multi-Analysis materialization. | Review the diagnostic and use the governed retry; do not repeat the business approval. |
| A retry appears to duplicate an artifact | Existing finalized state was not recognized. | Stop downstream use and reconcile artifact identity before continuing. |
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
