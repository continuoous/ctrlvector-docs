# Prepare and submit data

!!! warning "Page status: Draft"
    This chapter reflects the current Submission Workspace, Sub-Workspace, upload, and approval sequence. It requires product-owner and learner-clarity review before validation.

## Purpose

Submission transactions collect business contributions inside a governed Analysis, Version, and Period context. They separate contribution preparation from approval and finalization.

## Learning objectives

After reading this chapter, you should be able to:

- identify the prerequisites for a Submission Workspace;
- decide whether Sub-Workspaces are useful;
- follow the upload and approval sequence;
- recognize the saved artifact produced at each stage;
- prepare a Workspace for finalization.

## Prerequisites

Before starting, verify:

- the Analysis Definition in `ANA03`;
- the Version in `VER03`;
- the Period in `PER03`;
- the intended contributors and ownership;
- the contribution scope and expected source data.

## Submission transactions

| TCode | Business intention | Output |
|---|---|---|
| `SWS01` | Create Submission Workspace | Governed Version–Period Workspace |
| `SSW01` | Attach an Analysis Sub-Workspace | Analysis calculation and presentation scope within the Workspace |
| `SST01` | Create reference-based initial submissions | Submitted Contribution evidence |
| `SST02` | Revise selected Contributions | Updated, traceable submission evidence |
| `SST03` | Display submissions | Read-only submitted values and lineage |
| `SST09` | Upload initial values | Bulk-uploaded Contribution evidence |
| `SUBAP` | Approve Workspace | One authorized Workspace decision eligible for finalization |

## Create the Submission Workspace

1. Open `SWS01`.
2. Select the intended Version and Period.
3. Create the Workspace.
4. Confirm that the saved Workspace represents the intended submission context.

### Expected result

The Workspace becomes available to downstream submission transactions.

## Decide whether to use Sub-Workspaces

Use `SSW01` to attach each Analysis required by the Workspace.

1. Open `SSW01`.
2. Select the existing Workspace.
3. Create the required Analysis Sub-Workspace.
4. Repeat for each governed Analysis.

A Sub-Workspace carries an Analysis context. Governed Contributions may support more than one Analysis.

## Populate Workspace inputs

1. Open `SST01` for reference-based values or `SST09` for direct CSV upload.
2. Select the intended Version and Period Workspace.
3. Confirm expected Contributions are materialized.
4. Choose the permitted population strategy or upload route.
5. Review native-grain rows and validation feedback.
6. Confirm or override the proposed submission values.
7. Apply or commit the package.

Sub-Workspace, Contribution, and Dimension controls inside the review narrow what is displayed. Governed matching—not the review filter—determines which Contributions receive package evidence.

### Use reference data

If the Factor's Population Policy permits package strategies, process one Factor at a time in `SST01`. Verify the Reference Version, Reference Period rule, source values, calculation, and editable Draft submission before Apply.

When all calculated defaults are accepted unchanged, select **Use calculated defaults as submitted**. When a value must differ, edit the Draft explicitly so the submitted value and calculated default remain separately auditable.

See [Populate inputs from reference data](../implementation/reference-population.md) for strategy semantics and behavior checks.

### Expected result

The selected Workspace contains a submitted contribution that can be reviewed in `SUBAP`.

## Inspect and correct before approval

Use `SST03` to reopen Covered evidence as a read-only Submitted worksheet.

- Use `SST02` for one contributor's Factor scope.
- Use **Withdraw** when an entire package is invalid.
- Use **Resubmit** only when the frozen values remain valid.
- Use **Start replacement package** when corrected values or sources are required.

When names repeat, identify packages by row count, creation time, and short administrative reference. Wait for package-refresh progress to finish before performing another action.

## Approve the Workspace

1. Open `SUBAP`.
2. Select the Workspace containing complete submitted Contribution coverage.
3. Reconcile baseline and submitted Node results.
4. Review policy issues with their Factor, Contribution member scope, and contributor evidence.
5. Resolve each issue or route selected Contributions to `SST02`.
6. Recalculate after revision, then approve the complete Workspace once.

Approval is a separate control. Complete data coverage makes a Workspace calculable, not automatically acceptable. See [Govern submissions and approve the Workspace](../implementation/workspace-approval.md).

## Worked example

A monthly Actual Workspace contains contributions owned for Poland and Germany within one revenue Analysis.

1. Create one Workspace for the Actual Version and selected Period.
2. Attach the revenue Analysis as an Analysis Sub-Workspace.
3. Materialize the Poland and Germany Contribution scopes from their governed ownership groups.
4. Submit each country's values and verify their evidence.
5. Review the combined revenue result and approve the Workspace once.

### Expected result

The Workspace contains complete, clearly scoped Contributions and one current approval decision ready for `SUBFN`.

## Verification checklist

- The Workspace uses the correct Version and Period.
- Every Analysis Sub-Workspace has a clear business purpose.
- Each package is attached to the intended scope.
- Validation feedback has been resolved.
- Every policy issue is resolved or explicitly retained as an authorized open item.
- All Contributions intended for finalization are present and the current Workspace candidate is approved.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| `SWS01` needs setup | Version or Period is missing. | Verify `VER03` and `PER03`. |
| A Sub-Workspace cannot be created | No parent Workspace or Analysis exists. | Create the Workspace with `SWS01` and verify the Analysis in `ANA03`. |
| An upload appears under the wrong scope | The wrong Workspace or Sub-Workspace was selected. | Recheck the selected submission context before resubmitting. |
| The Workspace cannot be approved | Coverage is incomplete, a revision is pending, or the candidate is stale. | Return to the applicable SST transaction, then recalculate in `SUBAP`. |
| Apply is disabled although the preview is valid | No Draft value changed and unchanged defaults have not been acknowledged. | Change the intended Draft or select **Use calculated defaults as submitted**. |
| A statistical Mix preview cannot be applied | The generated Draft violates its compositional total. | Correct the Draft groups before Apply. |
| A package cannot be withdrawn | A linked Contribution is already approved. | Correct before approval; after approval, follow the governed reopening process for the release. |
| Finalization is not ready | The current Workspace candidate is not approved. | Complete the `SUBAP` review and Workspace decision. |

## Practice

Design a submission structure for three regional contributors and two Analyses. Identify the shared Version–Period Workspace, the two Analysis Sub-Workspaces, and which Contributions affect one or both Analyses.

## Knowledge check

1. Which two configured objects define a Workspace in `SWS01`?
2. What business object does `SSW01` attach?
3. Do SST transactions approve the Workspace?
4. Which transaction must be complete before finalization?

??? example "Answers"
    1. A Version and Period.
    2. An Analysis as a Sub-Workspace.
    3. No. The Workspace decision occurs in `SUBAP`.
    4. `SUBAP`.

## Related material

- [End-to-end workflow](end-to-end-workflow.md)
- [Populate inputs from reference data](../implementation/reference-population.md)
- [Finalize databases](finalize-databases.md)
- [Transaction-code reference](../reference/transaction-codes.md)
