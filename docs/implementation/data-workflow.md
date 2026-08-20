# Load and govern data

!!! warning "Page status: Draft"
    The workflow has been exercised with period-specific long-format files and governed reference-population packages. Confirm the current file contract and Population Policy before using it in another release.

## Entry criteria

- The effective Analysis scope is approved.
- Each uploaded Factor has a native grain and Data Collation Group.
- Expected row counts and numerical controls are documented.

## 1. Normalize to native grain

One business fact should appear once at its native coordinate. Leave non-native dimension fields blank; do not repeat a Product-grain value for every Country and Mode.

For the illustrative two-Country, two-Product, three-Mode model:

| Factor | Native grain | Facts per period |
| --- | --- | ---: |
| HW KUnits | Country + Product | 4 |
| MoT Mix | Country + Product + Mode | 12 |
| KG Per Unit | Product | 2 |
| Fill Rate | Country + Product + Mode | 12 |
| Rate Per KG | Country + Mode | 6 |
| **Total** |  | **36** |

The long-format package uses Version, Period, Factor, Value, and the applicable canonical Dimension columns. Derived Factors are not uploaded.

## 2. Run pre-upload controls

Before opening SUBST, verify:

- one Version and Period per package;
- expected counts by Factor;
- no duplicate Factor plus populated-dimension key;
- percentage representation is consistent;
- compositional groups reconcile, such as Mode Mix summing to `1.0`;
- repeated presentation values have been collapsed to one native fact;
- independent derived totals have been calculated.

## 3. Create period-specific workspaces

Use SUBWK to create the reference workspace first and the current workspace second. Select the approved Analysis, Version, and Period. Configure the comparison reference on the current workspace where required.

Use SUBSW only when contribution partitioning is needed.

## 4. Materialize and submit contributions

In SUBST:

1. select the Version–Period workspace;
2. materialize expected contributions from the configured Data Collation Groups;
3. reconcile the expected count to the design workbook;
4. choose direct upload or governed reference population;
5. review the package at each Factor's native grain;
6. resolve validation or behavior findings;
7. confirm or override Draft submissions;
8. apply or commit the accepted package.

A matching expected-contribution and accepted-row count is a strong control only when the design intentionally expects one row per contribution. Define the control for your own model rather than assuming `36` is universal.

### Reference-populated inputs

When the target Version meaning permits a package strategy, SUBST can create a governed zero-row reference package and calculate Draft values from finalized source Versions and Periods. Copy, Average, Maximum, Minimum, Median, uplift, and manual routes have different source requirements.

Review source context, calculation, native-grain Dimension combinations, and Draft submission before Apply. A statistical result may still violate a compositional or other cross-coordinate behavior constraint.

Use [Populate inputs from reference data](reference-population.md) for the complete strategy, correction, lineage, and control procedure.

## 5. Approve, finalize, and verify

Follow the governed sequence:

```text
SUBAP → SUBFN → SUBTV
```

- SUBAP confirms contribution decisions.
- SUBFN creates the finalized input database and materializes eligible derivations.
- SUBTV records the totals-verification state.

Repeat the complete sequence independently for the reference and current workspaces.

## Exit evidence

- [ ] Expected contributions reconcile by Factor and native coordinate
- [ ] Accepted package rows reconcile to the approved control
- [ ] Reference calculations, Draft overrides, and source lineage reconcile where used
- [ ] One authoritative submitted package supplies the intended Contribution coverage
- [ ] No unresolved exceptions remain
- [ ] All contributions are approved
- [ ] Finalization reports the expected record population
- [ ] Node totals reproduce the independent reference/current calculations
- [ ] SUBTV shows that totals verification has been recorded

See [Prepare and submit data](../operating/prepare-and-submit-data.md) and [Finalize databases](../operating/finalize-databases.md) for TCode-level guidance.

Next: [Create and validate the Bridge](bridge-validation.md).
