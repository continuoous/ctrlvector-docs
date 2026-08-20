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
| `SUBWK` | Create Submission Workspace | Governed Workspace for an Analysis, Version, and Period |
| `SUBSW` | Create Submission Sub-Workspace | Optional partition within the Workspace |
| `SUBST` | Populate Workspace Inputs | Submitted contribution evidence from uploaded or reference-populated values |
| `SUBAP` | Approve Contribution | Approved contribution eligible for finalization |

## Create the Submission Workspace

1. Open `SUBWK`.
2. Select the intended Analysis, Version, and Period.
3. Create the Workspace.
4. Confirm that the saved Workspace represents the intended submission context.

### Expected result

The Workspace becomes available to downstream submission transactions.

## Decide whether to use Sub-Workspaces

Use `SUBSW` when the Workspace needs clearer contribution partitions, such as separate business units, teams, or submission responsibilities.

1. Open `SUBSW`.
2. Select the existing Workspace.
3. Create the required Sub-Workspace.
4. Repeat for each governed partition.

A Sub-Workspace is recommended for clearer contribution scope but is not required for every upload.

## Populate Workspace inputs

1. Open `SUBST`.
2. Select the intended Version and Period Workspace.
3. Confirm expected Contributions are materialized.
4. Choose the direct upload or reference-population route in Workspace Upload Review.
5. Review native-grain rows and validation feedback.
6. Confirm or override the proposed submission values.
7. Apply or commit the package.

Sub-Workspace, Contribution, and Dimension controls inside the review narrow what is displayed. Governed matching—not the review filter—determines which Contributions receive package evidence.

### Use reference data

If the Factor's Population Policy permits package strategies, select **Start reference population** and process one Factor at a time. Verify the Reference Version, Reference Period rule, source values, calculation, and editable Draft submission before Apply.

When all calculated defaults are accepted unchanged, select **Use calculated defaults as submitted**. When a value must differ, edit the Draft explicitly so the submitted value and calculated default remain separately auditable.

See [Populate inputs from reference data](../implementation/reference-population.md) for strategy semantics and behavior checks.

### Expected result

The selected Workspace contains a submitted contribution that can be reviewed in `SUBAP`.

## Inspect and correct before approval

Use **View submitted values** to reopen Covered evidence as a read-only Submitted worksheet.

- Use **Revise selected Contribution** for one unapproved owner's Factor scope.
- Use **Withdraw** when an entire package is invalid.
- Use **Resubmit** only when the frozen values remain valid.
- Use **Start replacement package** when corrected values or sources are required.

When names repeat, identify packages by row count, creation time, and short administrative reference. Wait for package-refresh progress to finish before performing another action.

## Approve contributions

1. Open `SUBAP`.
2. Select the Workspace containing submitted contributions.
3. Review each contribution's status and scope.
4. Approve contributions that satisfy the business and validation requirements.
5. Reject or return contributions that require correction.
6. Confirm that the intended contributions are approved before continuing.

Approval is a separate control. Uploading a package does not make it eligible for finalization automatically.

## Worked example

A monthly Actual submission contains contributions from Poland and Germany.

1. Create one Workspace for the revenue Analysis, Actual Version, and selected Period.
2. Create `Poland` and `Germany` Sub-Workspaces.
3. Upload each country's Contribution Package to the matching Sub-Workspace.
4. Review both submissions.
5. Approve each eligible contribution.

### Expected result

The Workspace contains clearly scoped, approved contributions ready for `SUBFN`.

## Verification checklist

- The Workspace uses the correct Analysis, Version, and Period.
- Every Sub-Workspace has a clear business purpose.
- Each package is attached to the intended scope.
- Validation feedback has been resolved.
- Only eligible contributions are approved.
- All contributions intended for finalization are present and approved.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| `SUBWK` needs setup | Analysis, Version, or Period is missing. | Verify `ANA03`, `VER03`, and `PER03`. |
| A Sub-Workspace cannot be created | No parent Workspace exists. | Create and save the Workspace with `SUBWK`. |
| An upload appears under the wrong scope | The wrong Workspace or Sub-Workspace was selected. | Recheck the selected submission context before resubmitting. |
| A contribution cannot be approved | It is not submitted or still requires correction. | Return to `SUBST`, resolve the issue, and resubmit. |
| Apply is disabled although the preview is valid | No Draft value changed and unchanged defaults have not been acknowledged. | Change the intended Draft or select **Use calculated defaults as submitted**. |
| A statistical Mix preview cannot be applied | The generated Draft violates its compositional total. | Correct the Draft groups before Apply. |
| A package cannot be withdrawn | A linked Contribution is already approved. | Correct before approval; after approval, follow the governed reopening process for the release. |
| Finalization is not ready | Required contributions are not approved. | Complete the `SUBAP` review. |

## Practice

Design a submission structure for three regional contributors. Decide whether one Workspace with three Sub-Workspaces provides the clearest scope, and identify the Analysis, Version, and Period each contribution should share.

## Knowledge check

1. Which three configured objects are required for `SUBWK`?
2. Is `SUBSW` always mandatory?
3. Does `SUBST` approve a contribution?
4. Which transaction must be complete before finalization?

??? example "Answers"
    1. An Analysis, Version, and Period.
    2. No. It is optional but recommended when partitioning improves scope.
    3. No. Approval occurs in `SUBAP`.
    4. `SUBAP`.

## Related material

- [End-to-end workflow](end-to-end-workflow.md)
- [Populate inputs from reference data](../implementation/reference-population.md)
- [Finalize databases](finalize-databases.md)
- [Transaction-code reference](../reference/transaction-codes.md)
