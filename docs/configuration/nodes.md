# Configure nodes and calculation structures

!!! warning "Page status: Draft"
    The governed PR-node rules and Freight Spend example are verified. Broader Node maintenance and SV-node coverage still require validation.

## Chapter purpose

Teach configuration specialists how factors combine into governed business results.

## Planned coverage

- node purpose, identity, and supported relationships;
- factor roles and child-node structures;
- formulas, units, signs, and expected behavior;
- validation and exact reconciliation;
- create, change, display, and bulk package tasks;
- a numerical worked example and troubleshooting.

## PR nodes across different factor grains

A Product/Rate (`PR`) Node multiplies its ordered numerator factors and divides by any denominator factors. An unclustered Node with four factors produces up to `2⁴ − 1 = 15` pure and interaction terms. Those terms reconcile exactly to the Node's current value minus its baseline value.

Factors do not need to start at the same native grain when their Dimension Policy Templates explicitly govern every required expansion as `broadcast`. Bridge preparation uses the union of the factors' native dimensions only when all missing dimensions are covered. It then repeats each coarser value over matching full-grain combinations before decomposition.

For example:

| Factor | Native grain | Governed expansion |
| --- | --- | --- |
| HW KUnits | Country + Product | Broadcast to Mode of Transport |
| MoT Mix | Country + Product + Mode of Transport | None |
| Weight | Country + Product + Mode of Transport | None |
| Rate Per KG | Country + Mode of Transport | Broadcast to Product |

Configure the unclustered PR formula:

`Freight Spend = HW KUnits × MoT Mix × Weight × Rate Per KG`

The calculation grain becomes Country + Product + Mode of Transport. HW KUnits is repeated across the matching Mode members, and Rate Per KG is repeated across the matching Product members. MoT Mix and Weight remain exact at their native grain.

This produces four driver effects plus their interactions. If Weight is itself derived as `KG Per Unit ÷ Fill Rate`, BRRES treats Weight as one driver; it does not separately expose KG Per Unit and Fill Rate in this Node.

!!! failure "Partial broadcast governance"
    CtrlVector does not mix some broadcasts with unresolved grain differences. If any factor requests broadcast to the union grain, every missing dimension of every Node factor must be explicitly governed as `broadcast`. Missing target-grain facts, duplicate native coordinates, and absent matching source facts also stop preparation with an error.

!!! note "First PR factor"
    The first factor must reference a configured Factor, but it does not have to be classified as Additive merely to create the PR Node. Exact decomposition operates on the aligned row-level product. Features that specifically require an additive unit factor, such as certain per-unit views, remain unavailable unless the selected unit factor is genuinely Additive.
