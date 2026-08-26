# Transaction-code reference

!!! warning "Page status: Draft"
    This reference reflects the current standardized task families and operating transactions. It requires product-owner and learner-clarity review before validation.

A transaction code, or **TCode**, represents a business intention and an authorization boundary. Use a TCode when you know the object and activity you need to perform; use the Central Hub when you want CtrlVector to show setup and operating readiness.

Home and F3 return to VHOME and place keyboard focus in the primary **Open TCode** field, allowing the next transaction code to be entered immediately.

## Standard activity suffixes

Four suffixes are used consistently across the standardized configuration families:

| Suffix | Activity | Use it when |
|---|---|---|
| `01` | Create | Define a new governed object. |
| `02` | Change | Maintain an existing governed object. |
| `03` | Display | Inspect an object without changing it. |
| `09` | Import | Maintain one or more objects through a governed bulk task. |

## Foundation master data

| Family | Business object | Create | Change | Display | Import |
|---|---|---|---|---|---|
| DIM | Dimensions | `DIM01` | `DIM02` | `DIM03` | `DIM09` |
| CAL | Calendars | `CAL01` | `CAL02` | `CAL03` | `CAL09` |
| PER | Periods | `PER01` | `PER02` | `PER03` | `PER09` |
| VER | Versions | `VER01` | `VER02` | `VER03` | `VER09` |
| USR | Users / Owners | `USR01` | `USR02` | `USR03` | `USR09` |

Create Calendars before Periods because each Period belongs to a registered Calendar. Create the Version and Period required by a submission before opening its Workspace.

## Semantic model

| Family | Business object | Create | Change | Display | Import |
|---|---|---|---|---|---|
| DPT | Dimension Policy Templates | `DPT01` | `DPT02` | `DPT03` | `DPT09` |
| PPT | Population Policy Templates | `PPT01` | `PPT02` | `PPT03` | `PPT09` |
| FAC | Factors | `FAC01` | `FAC02` | `FAC03` | `FAC09` |
| NOD | Nodes | `NOD01` | `NOD02` | `NOD03` | `NOD09` |
| DLN | Decision Lenses | `DLN01` | `DLN02` | `DLN03` | `DLN09` |
| DLG | Decision Lens Groups | `DLG01` | `DLG02` | `DLG03` | `DLG09` |
| ALS | Aliases | `ALS01` | `ALS02` | `ALS03` | `ALS09` |
| OWN | Owner Assignments | `OWN01` | `OWN02` | `OWN03` | `OWN09` |

See [Dimension Policy Templates](../configuration/dimension-policy-templates.md) for the detailed DPT role, method, compatibility, numerical, and bulk-file guidance.

The main dependency order is:

1. create Dimensions before Dimension Policy Templates and Decision Lenses;
2. create reusable policy templates before Factors when they are part of the design;
3. create Factors before Nodes;
4. create Decision Lenses before Decision Lens Groups;
5. create Users / Owners before Owner Assignments;
6. create Aliases after the canonical objects they must resolve.

## Analysis definitions

| Family | Business object | Create | Change | Display | Import |
|---|---|---|---|---|---|
| ANA | Analysis Definitions | `ANA01` | `ANA02` | `ANA03` | `ANA09` |

An Analysis Definition requires registered Dimensions, Factors, and Nodes. Decision Lenses, Decision Lens Groups, and Owner Assignments add governed consumption and accountability when needed.

## Submission transactions

| TCode | Intention | Prerequisite |
|---|---|---|
| `SWS01/02/03/09` | Create, change, display, or import Submission Workspaces | Version and Period; comparison references are maintained separately |
| `SSW01/02/03/09` | Create, change, display, or import Analysis Sub-Workspaces | Submission Workspace and Analysis |
| `SST01` | Create initial reference-based submissions | Collecting Workspace with pending governed Contributions |
| `SST02` | Revise selected Contributions | Covered submission eligible for revision |
| `SST03` | Display submitted values and lineage | Existing submission evidence |
| `SST09` | Upload initial values in bulk | Collecting Workspace with pending governed Contributions |
| `SUBAP` | Approve the Workspace | Complete submitted Contribution coverage |
| `SUBFN` | Finalize approved Analysis artifacts | Approved Workspace |
| `SUBTV` | Verify Totals | Finalized Input Database |

Follow these transactions in order. A readiness indicator reflects saved business artifacts and prerequisites, not whether someone has merely opened a transaction.

## Run and Bridge transactions

| TCode | Intention | Current availability |
|---|---|---|
| `RUNCR` | Create Draft Run | **Unavailable:** no current task page; do not use as a procedure |
| `RUNSB` | Submit Official Run | **Unavailable:** no current task page; do not use as a procedure |
| `BRDGE` | Create Bridge ID | Available |
| `BRRES` | View Bridge Results | Available |

`BRDGE` requires eligible finalized comparison data and an Analysis. `BRRES` requires a completed Bridge.

## Central Hub

`VHOME` opens the Central Hub. Use it to:

- review setup and operating readiness;
- see which prerequisites are missing;
- open an available task;
- return to a neutral starting point between activities.

## Choosing the correct transaction

1. Identify the business object or operating stage.
2. Check its prerequisites in the Central Hub.
3. For a configuration object, choose Create, Change, Display, or Import.
4. For an operating task, follow the submission and Bridge sequence.
5. Use Display after a change or import to verify the saved result.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| A task reports missing setup | A required upstream object has not been saved. | Open the prerequisite Display transaction and confirm the object exists. |
| A Period task is not ready | No registered Calendar is available. | Configure and verify the Calendar first. |
| A Node task is not ready | Required Factors are missing. | Configure and verify Factors before Nodes. |
| An Analysis task is not ready | Dimensions, Factors, or Nodes are incomplete. | Complete the required semantic model and verify each registry. |
| A submission cannot advance | The preceding submission artifact is absent or incomplete. | Return to the previous transaction in the submission sequence. |
| `RUNCR` or `RUNSB` cannot be opened | Their current task pages are unavailable. | Do not treat them as active procedures; use the available submission and Bridge guidance. |

## Related material

- [End-to-end workflow](../operating/end-to-end-workflow.md)
- [Configure dimensions](../configuration/dimensions.md)
- [Prepare and submit data](../operating/prepare-and-submit-data.md)
- [Create bridges](../operating/create-bridges.md)
