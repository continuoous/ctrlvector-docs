# Create and validate the Bridge

!!! warning "Page status: Draft"
    The validation pattern is workshop-tested. Recalculate the acceptance values for your own business model.

## Entry criteria

Both Version–Period workspaces are finalized, their totals have been verified, and the current workspace references the intended comparison.

## 1. Create the Bridge

In BRDGE:

1. select the eligible workspace comparison;
2. confirm the Bridge type and Analysis;
3. add an optional business note;
4. create the Bridge;
5. record the persisted result-row count and completion message.

See [Create bridges](../operating/create-bridges.md) for detailed prerequisites.

## 2. Reconcile the movement

Start with independently calculated controls:

| Control | Reference | Current | Current minus reference |
| --- | ---: | ---: | ---: |
| Freight Spend example | 9,188.477160 | 9,824.653469 | 636.176309 |

BRRES must show the same total movement for the unfiltered Bridge. Do not proceed to interpretation when the basic movement does not reconcile.

## 3. Reconcile Factor attribution

BRRES separates:

- **Pure Impact** — the effect of changing one Factor while the others remain at reference values;
- **Mix Impact** — the Factor's allocated share of interaction terms;
- **Shapley Value** — Pure Impact plus allocated interaction effects.

The Factor Shapley values must sum to the displayed Bridge movement at the active scope. Apply a filter and repeat the reconciliation before trusting drill-down behavior.

## 4. Validate Factor detail

For at least one Factor:

1. open the Decision Lens drill-down;
2. confirm the bar chart and table agree;
3. select a Country, Product, or Mode member;
4. open Narrative Factor Detail and confirm it retains that scope;
5. switch among relevant dimension concentration views;
6. open the linked total to verify the unfiltered Factor summary.

See [Dimension drilldowns](../analysis/dimension-drilldowns.md) and [Read a bridge](../analysis/read-a-bridge.md).

## 5. Validate raw formula and lineage

Open a Raw Impact Formula for a coordinate that uses a governed cascade.

Confirm that:

- every formula component used in the calculation has a value;
- governed values are visibly identified;
- Finalized input facts show exact targets or governed source rows;
- source dimensions use business member values rather than technical identifiers;
- baseline and current source facts can both be traced.

## 6. Validate per-unit mode when required

Enable **Show Per Unit Impact** only when the Node has an approved unit Factor.

Check that:

- the **Per-unit denominator reference** reconciles `eligible impact ÷ current denominator = per-unit movement` for each Node;
- the denominator row identifies the unit Factor and active-scope quantity;
- the denominator reflects native units without counting broadcast copies multiple times;
- filtered views use the applicable denominator at that scope;
- standard Factor Analysis controls are disabled while per-unit mode is active;
- disabling per-unit mode restores the previous Factor Analysis state;
- per-unit totals are not added across Nodes that use different unit definitions.

## Completion checklist

- [ ] Reference, current, and movement controls reconcile
- [ ] Factor Shapley values sum to the movement
- [ ] At least one filtered scope reconciles
- [ ] Narrative detail respects the selected business scope
- [ ] Dimension concentration views are selectable
- [ ] Raw formula inputs trace to finalized exact or governed sources
- [ ] Per-unit denominator and interaction state are correct, when applicable
- [ ] Open defects and deferred capabilities are recorded separately

The implementation is ready for review when every applicable check has evidence, not merely when the Bridge reports success.
