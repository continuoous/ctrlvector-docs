# Configure dimension policy templates

!!! warning "Page status: Draft"
    This chapter reflects the current Dimension Policy Template workbench, factor-behavior rules, and Bridge grain behavior. It requires product-owner and learner-clarity review before validation.

## Purpose

A **Dimension Policy Template** (DPT) describes how a factor relates to governed dimensions. For each relevant dimension, it states either:

- the dimension exists in the factor's source data and may need to be rolled up; or
- the dimension is missing from the source data and any future expansion into it must follow an explicit rule.

The reusable template is created independently, then assigned to one or more factors. It protects calculations from adding values that should be retained, weighted, recomputed, or selected, and from silently inventing finer-grain values.

!!! info "Terms used in this chapter"
    **Governed dimension** means a registered business classification, such as Region, Product, Channel, or Period. **Grain** means the unique combination of dimension values represented by one fact, such as Region + Product. A **Bridge** is the calculation process that aligns configured factors at a shared grain and calculates their contribution to change.

## Learning objectives

After completing this chapter, you should be able to:

- explain `DPT01`, `DPT02`, `DPT03`, and `DPT09`;
- distinguish a native/source dimension from a missing/expansion target;
- select a rollup, expansion, and allocation driver consistently;
- determine whether a template is compatible with a factor behavior;
- reconcile every method using a numerical example;
- prepare the normalized DPT09 files;
- identify valid metadata that the current Bridge path does not yet execute.

## Prerequisites

Before creating a template:

1. Create the required dimensions with the DIM transaction family.
2. Define each factor's business meaning and intended behavior.
3. Identify the exact grain of the factor's source data.
4. Decide which missing dimensions may be requested at a finer grain.
5. Create any factor that will be used as an allocation driver.

Complete [Dimensions](dimensions.md) before this chapter. Read [Configure factors](factors.md) for the wider factor lifecycle.

## Transaction family

| Transaction | Intention | Permitted work |
|---|---|---|
| `DPT01` | Create | Create and save a new Dimension Policy Template. |
| `DPT02` | Change | Select, change, create, save, or delete a template. |
| `DPT03` | Display | Inspect a template without changing it. |
| `DPT09` | Import | Download templates or exports, upload normalized CSV files, preview them, and commit one or more templates. |

The four transactions are modes of the same governed object. `DPT03` is read-only. A template cannot be deleted while it is assigned to a factor.

## The four fields on a policy line

| Field | Business question |
|---|---|
| **Role** | Is the dimension present in the source, missing but governed, or Not configured? |
| **Rollup method** | If a native dimension is removed from the calculation grain, how is the value combined? |
| **Expansion method** | If a missing dimension is requested, what finer-grain interpretation is allowed? |
| **Allocation driver** | If a value is allocated, which factor supplies the relative weights? |

### Role controls which method is active

| Role | Meaning | Active field | Required inactive values |
|---|---|---|---|
| **Not configured** | The template declares no policy for this dimension. | None | No saved policy line |
| **Native/source dimension** | The dimension is present in the factor's source grain. | Rollup method | Expansion is `default`; allocation driver is empty |
| **Missing/expansion target** | The dimension is absent from the source grain but finer-grain use is governed. | Expansion method | Rollup is `default` |

There is no valid line with both a non-default rollup and a non-default expansion. Role makes them alternatives, not two independent calculations. Changing a role clears fields that no longer apply. Selecting an expansion other than `allocate` clears the allocation driver.

!!! tip "What factor source grain means"
    A factor's **source grain** is the level of dimensional detail at which that factor's data is provided to CtrlVector. If Freight Cost input records contain Region, Product, and Channel, its source grain is `Region x Product x Channel`, and those three dimensions are Native/source dimensions. A dimension absent from those input records is not part of the source grain; configure it as a Missing/expansion target when finer-grain use must be governed, or leave it Not configured when it is irrelevant to the policy. Source grain describes the incoming factor data before Bridge rollup or any planned expansion.

### Decision path for one dimension

```text
Is the dimension relevant to this template?
|
+-- No  -> Role = Not configured -> no saved policy line
|
+-- Yes -> Is it present in the factor's source grain?
          |
          +-- Yes -> Role = Native/source
          |          -> choose Rollup method
          |          -> Expansion = default
          |          -> Allocation driver = empty
          |
          +-- No  -> Role = Missing/expansion target
                     -> Rollup = default
                     -> choose Expansion method
                     -> if Allocate: driver required
                     -> otherwise: driver empty
```

## Two validation gates

A DPT is not tied to a factor behavior when it is created, so validation occurs in two stages.

### Gate 1: template structure

The DPT transaction checks that:

- the template has a unique name;
- each configured dimension exists and occurs only once;
- a native dimension does not define expansion;
- an expansion target does not define rollup;
- every method is supported;
- `allocate` has a known allocation-driver factor.

### Gate 2: assigned factor behavior

When the template is assigned, CtrlVector checks every method against the factor's behavior. A template containing `sum` can be structurally valid but cannot be assigned to a Ratio factor.

The same check protects existing assignments when a shared template changes. If a proposed change would make an assigned factor incompatible, the template is not saved.

!!! tip "Designing a reusable template"
    `default` is the most portable method because it delegates to the assigned factor behavior. Explicit methods are more precise, but narrow the behaviors that can use the template.

## Complete compatibility matrix

Each value in a method cell is a separate valid combination for that behavior and role. The inactive method must remain `default`.

| Factor behavior | Role | Valid active method | Allocation driver |
|---|---|---|---|
| Additive | Native/source | `default` or `sum` rollup | Empty |
| Additive | Missing/expansion target | `default`, `reject`, or `allocate` expansion‡ | Required only for `allocate`; otherwise empty |
| Invariant | Native/source | `default` or `retain` rollup | Empty |
| Invariant | Missing/expansion target | `default`, `reject`, or `broadcast` expansion‡ | Empty |
| Weighted rate | Native/source | `default` or `weighted_average` rollup | Empty |
| Weighted rate | Missing/expansion target | `default`, `reject`, or `broadcast` expansion‡ | Empty |
| Ratio | Native/source | `default` or `recompute_ratio` rollup | Empty |
| Ratio | Missing/expansion target | `default`, `reject`, `broadcast`, or `recompute` expansion‡ | Empty |
| Compositional share | Native/source | `default` or `recompute_share` rollup | Empty |
| Compositional share | Missing/expansion target | `default`, `reject`, `broadcast`, or `recompute` expansion‡ | Empty |
| Snapshot | Native/source | `default` or `select` rollup | Empty |
| Snapshot | Missing/expansion target | `default`, `reject`, or `broadcast` expansion‡ | Empty |
| Semi-additive | Native/source | `default`¶, `dimension_policy`†, `sum`, `retain`, `weighted_average`§, or `select` rollup | Empty |
| Semi-additive | Missing/expansion target | `default`, `reject`, `broadcast`, or `allocate` expansion‡ | Required only for `allocate`; otherwise empty |

For every behavior, **Not configured** is also valid and creates no policy line.

Unmarked method cells are executed as written by the current common-grain Bridge. Marked cells have the runtime caveat defined below.

¶ **Semi-additive `default`:** inherits the behavior-level `dimension_policy` marker, which is non-concrete; a dimension-specific policy must supply the operative method. A removal step still needs exactly one concrete supported method after `default` and `dimension_policy` values are ignored.

† **Explicit unresolved marker:** `dimension_policy` deliberately records that no concrete method is named on this line. A removal step hard-stops unless exactly one concrete supported method remains after unresolved markers are ignored.

‡ **Metadata only:** valid configuration, but the current common-grain Bridge does not execute expansion or create target rows.

§ **Hard stop if invoked:** `weighted_average` is a concrete and valid Semi-additive method, but the current Bridge cannot execute it as a Semi-additive rollup. Unlike ¶ and †, it is resolved in meaning but not yet implemented for this behavior. It can satisfy the exactly-one-concrete-method consistency check, then fail the implementation check during the same grain-preparation stage.

### What `default` means

`default` is inheritance, not another formula.

| Factor behavior | Native rollup inherited by `default` | Expansion inherited by `default` |
|---|---|---|
| Additive | `sum` | `reject` |
| Invariant | `retain` | `broadcast` |
| Weighted rate | `weighted_average` | `reject` |
| Ratio | `recompute_ratio` | `reject` |
| Compositional share | `recompute_share` | `reject` |
| Snapshot | `select` | `reject` |
| Semi-additive | Inherits the behavior-level `dimension_policy` marker, which is non-concrete; a dimension-specific policy must supply the operative method | `reject` |

When `default` resolves to the same method as an explicit choice, their governed meaning is the same and, where the method is currently executed, the calculation is the same. For example, Additive expansion `default` and explicit `reject` both mean reject. Choose:

- `default` when the template should inherit the assigned behavior and remain more portable;
- the explicit matching method when DPT03 should show that the decision was deliberately pinned and auditable.

An explicit method can become incompatible if the assigned factor behavior changes; `default` inherits the new behavior when the resulting assignment is otherwise valid.

!!! note "Similar names in different fields"
    `recompute_ratio` and `recompute_share` are **rollup_method** values. The shorter `recompute` is an **expansion_method** value used for either Ratio or Compositional share. These are exact current product and DPT09 values. Always read the CSV column as well as the method name; do not put `recompute` in `rollup_method` or `recompute_ratio` in `expansion_method`.

## Execution-status conventions

The current runtime has three distinct outcomes:

| Status used below | Operational result |
|---|---|
| **Executed** | The current common-grain Bridge performs the native rollup. |
| **Hard stop if invoked** | The configuration can be saved, but a Bridge that must use an unsupported, unresolved, or conflicting Semi-additive rollup fails at grain preparation. No result from that Bridge step is produced. |
| **Metadata only** | The expansion can be saved and behavior-validated, but the current common-grain Bridge does not attempt expansion. It can continue at its available grain; it neither creates target rows nor fails merely because the expansion line exists. |

The two not-yet-executed statuses differ: an unsupported Semi-additive rollup fails when required, while an expansion-target line is currently not invoked.

## Numerical explanation of every rollup

Rollup applies only to a **Native/source dimension**. In each example, that dimension is removed from the calculation grain.

### `sum`

Use for Additive values, or a Semi-additive factor on an additive dimension.

| Channel | Freight cost |
|---|---:|
| Air | 120 |
| Ocean | 80 |
| Surface | 50 |

Removing Channel gives `120 + 80 + 50 = 250`.

Valid behaviors: Additive and Semi-additive.

### `retain`

Use for an Invariant value, or a Semi-additive factor on a dimension across which one consistent value remains.

| Source row | Tax rate |
|---|---:|
| A | 5% |
| B | 5% |
| C | 5% |

The result is **5%**, not `15%`. If the values are `5%`, `5%`, and `6%`, current Bridge rollup rejects the conflict instead of choosing or averaging.

Valid behaviors: Invariant and Semi-additive.

### `weighted_average`

Use for a Weighted rate with its configured weight factor. It is also accepted as Semi-additive configuration, subject to the limitation below.

| Channel | Rate | Units |
|---|---:|---:|
| Air | 10.00 | 100 |
| Ocean | 20.00 | 300 |

`((10.00 x 100) + (20.00 x 300)) / (100 + 300)`

`= (1,000 + 6,000) / 400 = 17.50`

The simple average `(10.00 + 20.00) / 2 = 15.00` is incorrect. Units is the Weighted rate's weight factor, not the DPT allocation driver.

Valid behaviors: Weighted rate and Semi-additive.

!!! failure "Hard stop if invoked"
    `weighted_average` is accepted for Semi-additive configuration, but the current common-grain Bridge does not execute it as a Semi-additive rollup. If that dimension must be removed, grain preparation raises an unsupported-method error and that Bridge step produces no result.

### `recompute_ratio`

Use for a Ratio with configured numerator and denominator factors.

| Channel | Revenue | Units | Native ratio |
|---|---:|---:|---:|
| Air | 100 | 10 | `10.00` |
| Ocean | 300 | 20 | `15.00` |

Roll up components first:

`(100 + 300) / (10 + 20) = 400 / 30 = 13.3333`

The simple average `(10.00 + 15.00) / 2 = 12.50` is incorrect.

Valid behavior: Ratio.

### `recompute_share`

Use for a Compositional share with configured weight factor and composition dimension. Product A has Mode weights `20`, `50`, `30`; Product B has `10`, `20`, `20`.

| Mode | Combined weight | Recomputed share |
|---|---:|---:|
| Air | `20 + 10 = 30` | `30 / 150 = 20.000%` |
| Ocean | `50 + 20 = 70` | `70 / 150 = 46.667%` |
| Surface | `30 + 20 = 50` | `50 / 150 = 33.333%` |
| **Total** | **150** | **100.000%** |

Weights are combined before shares are calculated. If the composition dimension is removed, the current governed result is the configured expected total, normally `1.0` or 100%.

Valid behavior: Compositional share.

### `select`

Use for a Snapshot, or a Semi-additive factor on a snapshot-like dimension.

| Period | Closing headcount |
|---|---:|
| January | 100 |
| February | 120 |
| March | 110 |

Removing Period should select the governed final observation, **110**. Summing to `330` is invalid because these are successive states.

The current implementation sorts the internal native `idx` value as text and selects the final row in that order. It does not inspect a Period field or business timestamp to determine chronology. March is selected in this example only if the `idx` ordering places the March row last. If `idx` values are arbitrary identifiers, do not assume that `select` means chronologically latest; validate the source index contract before relying on the result.

Valid behaviors: Snapshot and Semi-additive.

### `dimension_policy`

`dimension_policy` is a valid Semi-additive rollup token that marks a native dimension as governed without naming a concrete operation. It performs no arithmetic and does not change DPT09 validation.

For example, this DPT09 line is structurally valid and can be assigned only to a Semi-additive factor:

```csv
template_name,dimension_name,role,rollup_method,expansion_method,allocation_driver_factor
Closing Inventory Grain,Region,native,dimension_policy,,
```

DPT03 saves and displays `dimension_policy` exactly as written. During current Bridge grain preparation, `default` and `dimension_policy` are excluded when CtrlVector identifies the concrete method for all dimensions removed in that step:

- if no concrete method remains, the step fails because no operation is defined;
- if exactly one concrete method remains, that one method is applied to the whole removal step, including dimensions marked `dimension_policy`;
- if more than one concrete method remains, the step fails because the methods conflict.

For example, if Region is `dimension_policy` and Period is `select`, removing Region alone fails, while removing both causes the whole step to use `select`. It does **not** mean "sum Region, then select Period."

Use `dimension_policy` only to record that the operative method is deliberately deferred. For a calculation-ready template, replace it with a concrete supported method such as `sum`, `retain`, or `select`.

For Closing inventory:

| Period | North | South |
|---|---:|---:|
| January | 60 | 40 |
| February | 70 | 50 |

After resolving the deferred policies, configure Region as `sum` and Period as `select`.

- Removing Region gives January `60 + 40 = 100` and February `70 + 50 = 120`.
- Removing Period while retaining Region selects February: North `70`, South `50`.
- The business-required two-stage result after removing both would sum within Period, then select February, producing `120`.

The current DPT model has no field for operation order. Worksheet row order and DPT09 CSV row order do not define calculation order. The current Bridge does not execute that two-stage rollup: all dimensions removed in one step must use one consistent supported method.

!!! failure "Hard stop if invoked"
    If one Bridge step finds no concrete method after ignoring `default` and `dimension_policy`, or finds more than one concrete method, grain preparation raises an error and the step produces no result. Retain a dimension or configure one consistent concrete supported method. Any future ordered-rollup semantics require a separate product design; this Draft does not infer them from source-grain nesting or line order.

## Numerical explanation of every expansion

!!! info "Executed exception: factor derivation broadcast"
    Ordinary common-grain Bridge expansion remains metadata only. During finalized-database preparation, however, a factor derivation can broadcast a coarser source across active leaf members before applying its configured operation. Every dimension missing from that source must be configured explicitly as **Missing/expansion target** with `broadcast`. This exception does not execute `reject`, `allocate`, or `recompute` as standalone expansion methods; Project Sum retains its separate dimension-removal contract.

Expansion applies only to a **Missing/expansion target** and describes the permitted finer-grain interpretation.

!!! caution "Metadata only"
    The current common-grain Bridge does not read expansion-target lines to create finer-grain facts. It can continue at the available common grain without creating target rows and without failing merely because an expansion line exists. The examples below define governed future semantics, not current expansion execution.

### `reject`

Legal provision is `500` at Region level. Channel is missing and has no defensible driver. The governed meaning of `reject` is that an expansion-capable workflow must stop instead of inventing Air `500` and Ocean `500`, or assuming an unsupported `250 / 250` split. The current common-grain Bridge does not attempt that request, so this metadata line itself does not fail the run.

Valid behaviors: all current behaviors.

**Runtime status - Metadata only:** the current Bridge does not invoke `reject` expansion, so this policy line does not currently stop a run or create target rows.

### `broadcast`

Tax rate is 5% for North. Broadcast gives:

| Channel | Region | Tax rate |
|---|---|---:|
| Air | North | 5% |
| Ocean | North | 5% |
| Surface | North | 5% |

The rows do not create a 15% business total. Rolling them up retains 5%.

For a Weighted rate, Ratio, Compositional share, or Snapshot, broadcast similarly repeats the governed aggregate interpretation. It does not recalculate from finer-grain components.

Valid behaviors: Invariant, Weighted rate, Ratio, Compositional share, Snapshot, and Semi-additive.

**Runtime status - Context dependent:** ordinary common-grain Bridge expansion remains metadata only. During finalized-database preparation, a factor derivation executes `broadcast` for a coarser source when every missing target dimension is explicitly governed as broadcast.

#### Worked example: additive by Country and Product, broadcast by Mode of Transport

`HW KUnits` is additive across Country and Product, but the same Country + Product value must be visible under every Mode of Transport. This makes the factor **Semi-additive**: its aggregation meaning changes by dimension.

In FAC02, configure the factor as follows:

| Field | Value |
|---|---|
| Factor | `HW KUnits` |
| Behavior | Semi-additive amount |
| Dimension policy | `Revenue Units` |
| Format | `#` or `KUnits` |
| Behavior references | None required |

Do not configure the factor as Invariant: that would incorrectly prevent summation across Country and Product. Do not configure it as Additive: Additive permits `sum`, `reject`, or `allocate`, but not `broadcast`.

The true source input is at Country + Product grain and contains four facts:

| Country | Product | HW KUnits |
|---|---|---:|
| Canada | Product A | 30 |
| Canada | Product B | 40 |
| United States | Product A | 70 |
| United States | Product B | 60 |

In DPT02, configure `Revenue Units` as follows:

| Dimension | Role | Rollup method | Expansion method | Allocation driver |
|---|---|---|---|---|
| Country | Native/source dimension | `sum` | `default` | Empty |
| Product | Native/source dimension | `sum` | `default` | Empty |
| Mode of Transport | Missing/expansion target | `default` | `broadcast` | Empty |

No allocation driver is used: broadcast repeats a governed value; it does not split the value by weights.

The source total is:

```text
30 + 40 + 70 + 60 = 200 HW KUnits
```

The governed broadcast interpretation is:

| Mode of Transport | Country | Product | HW KUnits |
|---|---|---|---:|
| Air | Canada | Product A | 30 |
| Ocean | Canada | Product A | 30 |
| Surface | Canada | Product A | 30 |
| Air | Canada | Product B | 40 |
| Ocean | Canada | Product B | 40 |
| Surface | Canada | Product B | 40 |
| Air | United States | Product A | 70 |
| Ocean | United States | Product A | 70 |
| Surface | United States | Product A | 70 |
| Air | United States | Product B | 60 |
| Ocean | United States | Product B | 60 |
| Surface | United States | Product B | 60 |

Each Mode of Transport view contains the same total:

```text
Air = 200
Ocean = 200
Surface = 200
```

The three mode totals are repeated views of one quantity. They must not be summed as `200 + 200 + 200 = 600`; the governed HW KUnits total remains `200`.

!!! warning "Confirm which table is the source"
    The four-row Country + Product table is the source in this design. The twelve-row table illustrates the intended result after broadcast. If all twelve rows are physically supplied as input, Mode of Transport is already present in the source grain and must instead be configured as Native/source with `retain`; otherwise an ordinary sum would triple-count the factor.

!!! note "Where this broadcast executes"
    A DPT does not manufacture rows by itself. CtrlVector executes the broadcast when a governed calculation needs a finer grain. This includes finalized-database preparation when HW KUnits is a coarser source of a **Product factor derivation**, such as `XFactory = HW KUnits × MoT Mix`, and Bridge node preparation when every node factor can be aligned to the union grain through explicit `broadcast` policies. Derived facts and Bridge preparation both retain source and broadcast lineage.

### `allocate`

Balance Sheet Release is `300`. Channel Units supplies weights:

| Channel | Units | Share | Allocated release |
|---|---:|---:|---:|
| Air | 60 | `60 / 100 = 60%` | `300 x 60% = 180` |
| Ocean | 30 | `30 / 100 = 30%` | `300 x 30% = 90` |
| Surface | 10 | `10 / 100 = 10%` | `300 x 10% = 30` |
| **Total** | **100** | **100%** | **300** |

The reconciliation is `180 + 90 + 30 = 300`. The allocation driver is mandatory and must identify a known factor.

Valid behaviors: Additive and Semi-additive.

**Runtime status - Metadata only:** the current Bridge does not currently produce the `180 / 90 / 30` allocation illustrated above.

### `recompute`

For Revenue per Unit:

| Channel | Revenue | Units | Recomputed ratio |
|---|---:|---:|---:|
| Air | 100 | 10 | `10.00` |
| Ocean | 300 | 20 | `15.00` |

The parent ratio is `400 / 30 = 13.3333`, but target recomputation gives `10.00` and `15.00`. Broadcasting `13.3333` would be a different policy.

For a Compositional share, target weights `20`, `50`, and `30` recompute to `20%`, `50%`, and `30%`.

`recompute` does not use the DPT allocation driver. Ratio components and Compositional-share weights are configured on the factor.

Valid behaviors: Ratio and Compositional share.

**Runtime status - Metadata only:** the current Bridge does not currently produce these recomputed target values.

## Do not confuse supporting factors

| Method | Supporting configuration | Where it belongs |
|---|---|---|
| `allocate` expansion | Factor supplying target shares | DPT expansion-target line |
| `weighted_average` rollup | Weight factor | Weighted rate factor configuration |
| Ratio rollup or expansion recomputation | Numerator and denominator factors | Ratio factor configuration |
| Share rollup or expansion recomputation | Weight factor, composition dimension, expected total, and tolerance | Compositional-share factor configuration |

Only `allocate` enables and requires **Allocation driver** in DPT.

## Create a template with DPT01

1. Open `DPT01`.
2. Enter a unique **Name** and a description identifying the intended grain.
3. Select a Role for every relevant dimension.
4. For Native/source dimensions, select a Rollup method.
5. For Missing/expansion targets, select an Expansion method.
6. For `allocate`, select an Allocation driver.
7. Compare the design with the matrix for every intended factor.
8. Choose **Save**.
9. Open `DPT03` and verify the saved template.

CtrlVector confirms the save and displays the persisted lines.

## Change or delete with DPT02

1. Open `DPT02` and select a template.
2. Review its complete dimension worksheet.
3. Make the changes and recheck assigned factor behaviors.
4. Choose **Save**.
5. Open `DPT03` and verify the result.

Saving replaces the complete set of policy lines. A line changed to **Not configured** is removed.

The saved template name cannot currently be changed in DPT02. Update its description or policy lines, or create a new template when a different name is required.

Choose **Delete** only when the template is no longer needed. Deletion is rejected while factors reference it. `DPT02` also provides **New** for creating another template.

## Inspect with DPT03

1. Open `DPT03` and select a template.
2. Confirm its name and description.
3. Review every role and its active method.
4. Confirm the driver on every `allocate` line.
5. Compare the template with each assigned factor behavior.

`DPT03` has no Save or Delete action and its worksheet is read-only.

## Maintain templates with DPT09

DPT09 separates template-grain and policy-line-grain data.

| Data object | Grain | File type |
|---|---|---|
| `MDDPT` | One template header per row | CSV |
| `MDPOL` | One configured dimension role within one template per row | CSV |

`MDDPT` columns:

```text
template_name,description
```

`MDPOL` columns:

```text
template_name,dimension_name,role,rollup_method,
expansion_method,allocation_driver_factor
```

Use `native` or `expansion` for `role`. A Not-configured dimension has no MDPOL row.

- Native: supply `rollup_method`; leave expansion and driver empty.
- Expansion: leave rollup empty; supply `expansion_method`; supply a driver only for `allocate`.
- A blank applicable method cell and the literal value `default` are equivalent: both save the applicable method as `default`.

Example `MDDPT`:

```csv
template_name,description
Operating Amount Grain,Amounts reported by Region and Product with governed Channel allocation
```

Example `MDPOL`:

```csv
template_name,dimension_name,role,rollup_method,expansion_method,allocation_driver_factor
Operating Amount Grain,Region,native,sum,,
Operating Amount Grain,Product,native,sum,,
Operating Amount Grain,Channel,expansion,,allocate,Channel Units
```

### Bulk procedure

1. Choose **Template** for examples or **Export** for saved configuration.
2. Maintain unique headers in `MDDPT` and policy lines in `MDPOL`.
3. Use recognized business names for dimensions and drivers.
4. Upload both files.
5. Review the staged counts and resolve every error.
6. Choose **Commit**.
7. Open `DPT03` and verify each template.
8. Assign the template to its factor and confirm behavior compatibility.

Upload stages data; Commit persists it. A header name already in CtrlVector updates that template, and its submitted MDPOL rows become the complete line set. A new header creates a template.

## Verification checklist

- The name is unique and the description states the intended use.
- Every source-grain dimension is Native/source.
- Every expansion target is genuinely absent from the source grain.
- Native rows have only an allowed rollup.
- Expansion rows have only an allowed expansion.
- Every `allocate` row has the correct driver.
- No other row retains a driver.
- Every assigned factor passes behavior compatibility.
- Semi-additive dimensions that may be removed use concrete, currently executable methods.
- `DPT03` displays the expected persisted result.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| Incompatible rollup or expansion | The explicit method conflicts with an assigned behavior. | Use `default` or a method allowed by the matrix. |
| Allocation driver is required | `allocate` has no driver. | Select a known driver or another expansion. |
| Driver disappears | Expansion changed away from `allocate`, or role changed. | This is intentional; drivers apply only to allocation. |
| Delete is rejected | A factor still uses the template. | Remove or replace assignments first. |
| DPT09 reports an unknown dimension or driver | A business name does not resolve. | Correct the name or create the prerequisite object. |
| DPT09 reports a missing header | MDPOL refers to a name absent from uploaded MDDPT. | Add or correct the header. |
| Valid Semi-additive setup blocks a Bridge | Removed dimensions use mixed, unresolved, or currently unsupported methods. | Retain a dimension or use one consistent supported method in that step. |
| Expansion does not change results | A DPT is governance metadata until a supported calculation invokes it. Governed `broadcast` executes for factor derivations and compatible multi-factor Bridge nodes; other contexts may not expand rows. | Confirm the execution context. Do not expect standalone, `reject`, `allocate`, or `recompute` expansion to create rows. |

## Practice

Create an Additive template with Region and Product as native `sum` dimensions and Channel as an `allocate` target using Channel Units.

For source value `400` and driver values Air `50`, Ocean `30`, Surface `20`, verify:

- Air: `400 x 50% = 200`
- Ocean: `400 x 30% = 120`
- Surface: `400 x 20% = 80`
- total: `200 + 120 + 80 = 400`

!!! caution "Paper/governance exercise"
    The `400 -> 200 / 120 / 80` split tests the policy's intended arithmetic and reconciliation only. The current Bridge does not execute the allocation or produce these Channel rows.

Then design Closing inventory as Semi-additive: Region `sum`, Period `select`. With January North `40`, South `60`, and February North `55`, South `65`:

- removing Region gives January `100` and February `120`;
- removing Period while retaining Region gives North `55` and South `65`;
- removing both in one current Bridge step is unsupported because the methods differ.

## Knowledge check

1. Why can a DPT be structurally valid but incompatible with a factor?
2. Can one line use both `sum` and `allocate`?
3. What does native `default` mean for a Ratio?
4. Which expansion requires the DPT Allocation driver?
5. How do `broadcast` and `recompute` differ for a Ratio?
6. Why is `dimension_policy` not a complete arithmetic instruction?
7. When does DPT09 data become persistent?
8. Which expansion path is currently executed?
9. How does current `select` determine which source row is final?
10. How does **Hard stop if invoked** differ from **Metadata only**?

Select **Answers** to reveal the response key.

??? example "Answers"
    1. Creation has no factor behavior; assignment applies behavior-specific validation.
    2. No. Native and expansion are alternative roles.
    3. It inherits `recompute_ratio`.
    4. `allocate`.
    5. Broadcast repeats the aggregate value; recompute recalculates from target numerator and denominator facts.
    6. It marks Semi-additive governance but does not select a concrete operation.
    7. On Commit of a valid staged preview.
    8. Governed `broadcast` executes during finalized-database preparation when a coarser source feeds a factor derivation and every missing target dimension is explicitly configured for broadcast. Ordinary common-grain Bridge expansion and the other expansion methods remain unexecuted.
    9. It sorts the internal native `idx` as text and takes the last row; it does not interpret Period or a timestamp.
    10. A required unsupported Semi-additive rollup raises an error and stops the Bridge step. Expansion metadata is not invoked, so the Bridge may continue without creating expanded rows.

## Related material

- [Configure dimensions](dimensions.md)
- [Configure factors](factors.md)
- [Transaction-code reference](../reference/transaction-codes.md)
- [File and field reference](../reference/file-and-field-reference.md)
- [Create bridges](../operating/create-bridges.md)

## Product applicability

This Draft applies to the current Dimension Policy Template workbench, `MDDPT`/`MDPOL` bulk contract, factor-behavior compatibility rules, native-dimension common-grain Bridge behavior, and governed factor-derivation broadcast reviewed on 7 August 2026. Revalidate it when DPT fields, compatibility rules, Semi-additive execution, or finer-grain expansion behavior changes.
