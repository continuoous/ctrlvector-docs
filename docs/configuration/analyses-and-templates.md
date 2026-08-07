# Configure analyses and templates

!!! warning "Page status: Draft"
    Factor-scope dependency handling is verified. Broader Analysis lifecycle, ownership, and bulk-maintenance coverage still require validation.

## Chapter purpose

Show how reusable configuration is assembled into a governed analytical purpose.

## Planned coverage

- analysis identity, scope, dimensions, nodes, and factors;
- reusable templates and instantiated analyses;
- required inputs and submission schema;
- status, ownership, and lifecycle;
- validation, export, import, and reproducibility.

## Direct factors, dependencies, and uploads

Selecting a Node establishes its **analytical factors**: the factors that appear directly in the Node formula. If one of those factors is derived, the Analysis must also include the transitive source factors needed to calculate it. CtrlVector labels these separately as **supporting dependencies**.

Required uploads are a third concept. A derived analytical factor belongs to the effective Analysis scope but is not uploaded when its population policy calculates it from supplied inputs.

For example, suppose Freight Spend uses:

`HW KUnits × MoT Mix × Weight × Rate Per KG`

and Weight is derived as:

`KG Per Unit ÷ Fill Rate`

The resulting scope is:

| Category | Factors | Count |
| --- | --- | ---: |
| Analytical factors | HW KUnits, MoT Mix, Weight, Rate Per KG | 4 |
| Supporting dependencies | KG Per Unit, Fill Rate | 2 |
| Effective factors | All analytical factors and supporting dependencies | 6 |
| Required uploads | HW KUnits, MoT Mix, KG Per Unit, Fill Rate, Rate Per KG | 5 |

Selecting the Freight Spend Node automatically discovers KG Per Unit and Fill Rate through Weight's derivation. Factors unrelated to the selected Node or its dependency chain should not be added merely because they exist in the tenant.

!!! note "Published snapshots"
    A published template and its instantiated Analysis ID are immutable lineage snapshots. Correct a scope by creating and publishing a new versioned template, then instantiate a new Analysis ID. Do not silently mutate the existing published snapshot or create Submission Workspaces against a superseded scope.
