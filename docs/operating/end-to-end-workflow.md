# End-to-end workflow

!!! warning "Page status: Draft"
    This workflow reflects the current transaction dependencies and available task pages. It requires product-owner and learner-clarity review before validation.

## Purpose

CtrlVector separates reusable configuration from submitted business data and analytical results. The operating sequence ensures that each downstream object can resolve the governed objects it depends on.

## Learning objectives

After reading this chapter, you should be able to:

- describe the five stages of the CtrlVector workflow;
- identify the required handoff between stages;
- choose the next TCode from saved readiness evidence;
- distinguish available Bridge tasks from unavailable Run tasks;
- verify that an operating cycle has reached a reviewable result.

## Workflow at a glance

```text
Foundation master data
        ↓
Reusable semantic model
        ↓
Analysis Definition
        ↓
Submission, approval, finalization, and totals verification
        ↓
Bridge creation and result review
```

The Central Hub evaluates readiness from persisted business artifacts. Opening a page does not satisfy a prerequisite.

## Stage 1: establish foundation master data

Configure the shared objects that give later work its business context:

| Object | TCode family | Important relationship |
|---|---|---|
| Dimensions | `DIM` | Referenced by policies, lenses, aliases, and analyses |
| Calendars | `CAL` | Required before Periods |
| Periods | `PER` | Required by Submission Workspaces |
| Versions | `VER` | Required by Submission Workspaces |
| Users / Owners | `USR` | Required before Owner Assignments |

Use each family's `03` transaction to verify the saved result before continuing.

## Stage 2: build the semantic model

Create the reusable definitions that explain how facts are classified, calculated, grouped, and owned:

1. Configure Dimension Policy Templates (`DPT`) and Population Policy Templates (`PPT`) where the Factor design uses them.
2. Configure Factors (`FAC`).
3. Configure Nodes (`NOD`) from registered Factors.
4. Configure Decision Lenses (`DLN`) from registered Dimensions.
5. Configure Decision Lens Groups (`DLG`) from saved Decision Lenses.
6. Configure Aliases (`ALS`) after the canonical objects they resolve.
7. Configure Owner Assignments (`OWN`) after Users / Owners exist.

Some relationships are recommendations rather than hard prerequisites. For example, reusable policy templates simplify Factor setup, while Decision Lenses and ownership improve governed use of analyses.

## Stage 3: define the analysis

Use the `ANA` family to create and maintain the Analysis Definition.

The required foundation is:

- Dimensions;
- Factors;
- Nodes.

Decision Lenses, Decision Lens Groups, and Owner Assignments are recommended when the Analysis needs governed views and accountability.

Verify the saved definition with `ANA03`.

## Stage 4: collect and govern input data

Follow the submission chain:

```text
SUBWK → optional SUBSW → SUBST → SUBAP → SUBFN → SUBTV
```

| Stage | Result |
|---|---|
| `SUBWK` | A Submission Workspace for an Analysis, Version, and Period |
| `SUBSW` | An optional partition of the Workspace |
| `SUBST` | Submitted Contribution evidence from direct upload or governed reference population |
| `SUBAP` | An approved contribution |
| `SUBFN` | A finalized Input Database |
| `SUBTV` | Recorded totals verification |

Do not skip approval or finalization gates simply because later transactions are visible. Readiness depends on the saved result of the preceding stage.

## Stage 5: create and review a Bridge

Use `BRDGE` to create a governed comparison from eligible finalized data and an Analysis. Use `BRRES` to load and inspect the persisted Bridge result.

The catalog also contains `RUNCR` and `RUNSB`, but their task pages are currently unavailable. They must not be treated as active operating procedures. Bridge creation currently requires finalized input data; an official Run is not a hard prerequisite.

## End-to-end procedure

1. Open `VHOME` and review readiness.
2. Complete the foundation registries and verify them through their Display transactions.
3. Build the semantic model in dependency order.
4. Create and verify the Analysis Definition.
5. Create the Submission Workspace for the required Analysis, Version, and Period.
6. Create Sub-Workspaces when contributions need separate ownership or scope.
7. Upload and review Contribution Packages. For reference-populated inputs, calculate and review each Factor strategy before Apply.
8. Approve eligible contributions.
9. Finalize the Input Database.
10. Verify finalized totals.
11. Create the Bridge from eligible finalized data.
12. Open the Bridge result and verify its context before interpretation.

## Worked workflow example

Assume a business wants to compare Actual revenue between two monthly Periods.

1. Confirm the revenue Dimensions, Calendar, monthly Periods, Actual Version, and responsible Users / Owners.
2. Confirm the Factors and Nodes used by the revenue Analysis.
3. Create or select the Analysis Definition.
4. Open one Submission Workspace for each required Version and Period context.
5. Collect, approve, and finalize the contributions.
6. Record totals verification for each finalized Input Database.
7. Create the Bridge using the eligible comparison and Analysis.
8. Open `BRRES`, confirm the baseline and current context, then begin result review.

### Expected result

The Bridge is available for review with a traceable path back to governed configuration and finalized input data.

## Verification checklist

- Each prerequisite appears as saved and configured in the Central Hub.
- Periods belong to the intended Calendar.
- The Analysis uses the intended Dimensions, Factors, and Nodes.
- The Submission Workspace uses the intended Analysis, Version, and Period.
- Required contributions are approved.
- The Input Database is finalized.
- Totals verification is recorded.
- The Bridge uses the intended comparison and Analysis.
- `BRRES` displays the expected Bridge context.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| A downstream task needs setup | One or more required upstream artifacts are missing. | Follow the prerequisite explanation in the Central Hub. |
| A Workspace cannot be created | Analysis, Version, or Period setup is incomplete. | Verify `ANA03`, `VER03`, and `PER03`. |
| Finalization cannot proceed | Contributions have not completed approval. | Return to `SUBAP` and resolve the approval state. |
| Totals cannot be verified | No finalized Input Database exists. | Complete `SUBFN` first. |
| A Bridge candidate is unavailable | Eligible finalized comparison data or the Analysis is missing. | Verify the finalized inputs and Analysis selection. |
| A Run TCode cannot be opened | The current Run task pages are unavailable. | Do not use `RUNCR` or `RUNSB` as procedures. |

## Knowledge check

1. Why does opening a TCode not satisfy readiness?
2. Which three object families are required before an Analysis Definition?
3. Which submission step creates the finalized Input Database?
4. What must exist before `BRRES` can show a result?
5. Are `RUNCR` and `RUNSB` currently usable procedures?

??? example "Answers"
    1. Readiness is based on saved artifacts and dependencies, not page visits.
    2. Dimensions, Factors, and Nodes.
    3. `SUBFN`.
    4. A completed Bridge created through `BRDGE`.
    5. No. Their current task pages are unavailable.

## Related material

- [Transaction-code reference](../reference/transaction-codes.md)
- [Prepare and submit data](prepare-and-submit-data.md)
- [Populate inputs from reference data](../implementation/reference-population.md)
- [Finalize databases](finalize-databases.md)
- [Create bridges](create-bridges.md)
- [Review results](review-results.md)
