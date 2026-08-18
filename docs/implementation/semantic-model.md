# Configure the semantic model

!!! warning "Page status: Draft"
    This grain-first sequence reflects current verified behavior and requires implementation-lead review before validation.

## Entry criteria

Foundation Dimensions, Periods, and Versions have passed display-mode verification.

## 1. Define dimension policies

Create reusable Dimension Policy Templates before Factors. For each Factor, classify every Analysis Dimension as:

- **Native/source dimension** when the Factor is entered at that dimension;
- **Missing/expansion target** when a governed method must make the Factor available at that dimension.

The source grain is the grain at which the Factor data input happens. Do not infer it from the full-grain report where a value may have been repeated for presentation.

The workshop required these reusable patterns:

| Template pattern | Native dimensions | Governed expansion |
| --- | --- | --- |
| All dimensions native | Country + Product + Mode | None |
| Country and Product native | Country + Product | Broadcast to Mode |
| Product native | Product | Broadcast to Country and Mode |
| Country and Mode native | Country + Mode | Broadcast to Product |

See [Dimension Policy Templates](../configuration/dimension-policy-templates.md) for the compatibility matrix and runtime status of each method.

## 2. Define population policies

Separate uploaded leaf Factors from derived Factors. The workshop used one policy for contributor-uploaded Actual inputs and another for Factor derivation.

## 3. Create Factors in dependency order

Create source Factors before the derived Factors that depend on them. Keep Factor behavior separate from derivation logic: behavior governs aggregation, while Product or Ratio defines how a Factor is calculated.

An illustrative chain is:

```text
HW KUnits × MoT Mix = XFactory
KG Per Unit ÷ Fill Rate = Weight
XFactory × Weight = Tonnage
Tonnage × Rate Per KG = Freight Spend
```

Assign the approved Format, leaf role, behavior, Dimension Policy Template, Population Policy Template, Data Collation Group, and Analysis Group. Use FAC03 to review the consolidated register.

See [Factors](../configuration/factors.md) for Product, Ratio, weighted-rate, and Format guidance.

## 4. Create the analytical Node

Choose Node Factors that expose the controllable drivers users need to explain. A Freight Spend PR Node can use:

```text
HW KUnits × MoT Mix × Weight × Rate Per KG
```

This retains four analytical drivers while KG Per Unit and Fill Rate remain supporting dependencies inside Weight. When Factors have different native grains, every missing Node dimension must be explicitly and compatibly governed; otherwise Bridge preparation should stop rather than guess.

Verify the saved formula and operators in NOD03. See [Nodes and calculation structures](../configuration/nodes.md).

## 5. Create the Analysis

Scope the Analysis to the required Dimensions and Node. Selecting the Node should identify:

- analytical Factors used directly by the Node;
- supporting derivation dependencies;
- uploaded leaf Factors required from contributors.

Do not add every registered Factor merely because it is available. Review the effective scope in the Analysis display transaction.

## Exit evidence

- [ ] Every uploaded Factor has the correct native-grain policy
- [ ] Every cross-grain expansion is explicit and compatible
- [ ] Every derived Factor resolves its sources in dependency order
- [ ] The Node formula exposes the intended business drivers
- [ ] Analysis scope separates analytical Factors, supporting dependencies, and required uploads
- [ ] Data Collation and Analysis grouping prerequisites are complete

Next: [Load and govern data](data-workflow.md).

