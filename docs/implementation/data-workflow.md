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

Before opening `SST01` or `SST09`, verify:

- one Version and Period per package;
- expected counts by Factor;
- no duplicate Factor plus populated-dimension key;
- percentage representation is consistent;
- compositional groups reconcile, such as Mode Mix summing to `1.0`;
- repeated presentation values have been collapsed to one native fact;
- independent derived totals have been calculated.

## 3. Create period-specific workspaces

Use `SWS01` to create the reference Workspace first and the current Workspace second. Select the Version and Period, then use `SWS02` to maintain an optional comparison or submission baseline while the Workspace is still collecting.

Use `SSW01` to attach each required Analysis as a Sub-Workspace. A Sub-Workspace is an Analysis context, not merely a contribution partition.

## 4. Materialize and submit contributions

Use `SST01` for governed reference-based initial submissions or `SST09` for direct bulk upload:

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

When the target Version meaning permits a package strategy, `SST01` can create governed reference-based Draft values from finalized source Versions and Periods. Copy, Average, Maximum, Minimum, Median, and uplift routes have different source requirements. Use `SST09` for direct CSV upload.

Review source context, calculation, native-grain Dimension combinations, and Draft submission before Apply. A statistical result may still violate a compositional or other cross-coordinate behavior constraint.

Use [Populate inputs from reference data](reference-population.md) for the complete strategy, correction, lineage, and control procedure.

Use `SST02` for a targeted revision and `SST03` to inspect the currently submitted values and lineage without editing them.

## 5. Approve, finalize, and verify

Follow the governed sequence:

```text
SUBAP → SUBFN → SUBTV
```

- SUBAP evaluates policy issues and makes one decision over the complete recombined Workspace.
- SUBFN materializes one immutable artifact for each approved Analysis Sub-Workspace; it is not a second business approval gate.
- SUBTV records the totals-verification state.

When one submitted Factor affects several Analyses, SUBAP keeps those consequences together as one business event while preserving each Node's own format and boundary. After approval, all ready Analysis artifacts are materialized from that same Workspace decision. A repeated technical finalization request must not duplicate completed artifacts or reopen approval.

Finalized artifacts should retain the approved explanations, policy resolutions, residual items, mitigations, action owners, and due dates needed for downstream narrative and audit use.

See [Govern submissions and approve the Workspace](workspace-approval.md) for contributor preflight, structured evidence, targeted revision, conditional approval, and multi-Analysis guidance.

Repeat the complete sequence independently for the reference and current workspaces.

## Exit evidence

- [ ] Expected contributions reconcile by Factor and native coordinate
- [ ] Accepted package rows reconcile to the approved control
- [ ] Reference calculations, Draft overrides, and source lineage reconcile where used
- [ ] One authoritative submitted package supplies the intended Contribution coverage
- [ ] No unresolved exceptions remain
- [ ] All contributions are approved
- [ ] Finalization reports the expected record population
- [ ] One finalized artifact exists for each required Analysis
- [ ] Every artifact traces to the same approved Workspace candidate
- [ ] Approved narrative and action context remains traceable after finalization
- [ ] Node totals reproduce the independent reference/current calculations
- [ ] SUBTV shows that totals verification has been recorded

See [Prepare and submit data](../operating/prepare-and-submit-data.md) and [Finalize databases](../operating/finalize-databases.md) for TCode-level guidance.

Next: [Create and validate the Bridge](bridge-validation.md).
