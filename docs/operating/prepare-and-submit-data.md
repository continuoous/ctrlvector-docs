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
| `SUBST` | Upload Contribution Package | Submitted contribution in the selected scope |
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

## Upload a Contribution Package

1. Open `SUBST`.
2. Select the intended Workspace and, where used, Sub-Workspace.
3. Select the contribution scope.
4. Upload the Contribution Package.
5. Review the resulting contribution and any validation feedback.
6. Correct and resubmit data that is not ready for approval.

### Expected result

The selected Workspace contains a submitted contribution that can be reviewed in `SUBAP`.

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
- [Finalize databases](finalize-databases.md)
- [Transaction-code reference](../reference/transaction-codes.md)
