# Plan the implementation

!!! warning "Page status: Draft"
    This discovery method requires product-owner and implementation-lead review before validation.

## Start with the comparison question

Write the decision you want CtrlVector to explain in one sentence. A useful statement names:

- the result being explained;
- the reference and current Version–Period combinations;
- the dimensions at which users need to investigate the change;
- the business drivers expected to explain it.

Example: “Explain the Quarter-over-Quarter change in Freight Spend for Actuals by Country, Product, Mode of Transport, and controllable cost drivers.”

## Discovery questionnaire

Complete these questions before creating configuration.

### Business scope

1. Which result or KPI will the Bridge explain?
2. Which two Version–Period workspaces will be compared?
3. Which future periods should be configured now so the model remains reusable?
4. Which dimensions and leaf members define the required investigation grain?

### Factor design

For every Factor, record:

- business definition and display Format;
- whether it is uploaded or derived;
- native source dimensions;
- behavior when dimensions are removed;
- permitted expansion across missing dimensions;
- derivation formula and dependencies;
- population method;
- contribution owner or Data Collation Group;
- Analysis Group required for analytical drill-down.

### Data and controls

1. Which columns identify Version, Period, Factor, and dimensions?
2. Are percentages supplied as decimals or percentage points?
3. How many native-grain facts should exist for each Factor and period?
4. Which group totals must reconcile, such as a mix summing to `1.0`?
5. What independently calculated reference total, current total, and variance will be used to accept the result?

## Grain worksheet

Do not use “all dimensions” as a shortcut when a Factor is genuinely entered at a coarser grain.

| Factor | Population | Native dimensions | Missing-dimension treatment | Format |
| --- | --- | --- | --- | --- |
| HW KUnits | Uploaded | Country + Product | Broadcast to Mode | KUnits |
| MoT Mix | Uploaded | Country + Product + Mode | None | % |
| KG Per Unit | Uploaded | Product | Broadcast to Country and Mode | Kg/Unit |
| Fill Rate | Uploaded | Country + Product + Mode | None | % |
| Rate Per KG | Uploaded | Country + Mode | Broadcast to Product | USD/Kg |

## Acceptance plan

Define a control for every handoff:

- configuration: saved objects equal the approved design;
- Analysis: analytical Factors and supporting dependencies are complete;
- upload: accepted facts equal the native-grain expectation;
- finalization: derived results reproduce independent calculations;
- Bridge: current minus reference equals the displayed movement;
- BRRES: Factor Shapley values sum to the Bridge movement;
- audit: filtered detail and source lineage remain traceable.

## Exit checklist

- [ ] Business comparison sentence approved
- [ ] Version, Calendar, Period, and member scope approved
- [ ] Factor grain and policy worksheet complete
- [ ] Derivation dependency order complete
- [ ] Contribution ownership complete
- [ ] Expected row counts and numerical controls complete
- [ ] Deferred production requirements recorded separately

Next: [Build the foundation](foundation.md).
