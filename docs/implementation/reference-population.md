# Populate inputs from reference data

!!! warning "Page status: Draft"
    This chapter is based on a completed development-environment implementation workshop. It requires product-owner and implementation-lead review before validation.

## Purpose

Reference population creates a new Version and Period input package from finalized facts that already exist in CtrlVector. It is useful for Forecasts, plans, simulations, and controlled carry-forward processes where users want a governed starting point without uploading every value again.

Reference population is not an automatic copy. The user still chooses the source rule, reviews the calculated values, confirms or overrides the Draft submission, and completes the normal contribution, approval, finalization, and totals-verification controls.

## Verified applicability

The verified workshop used:

- a Forecast target for FY26-Q3;
- finalized Actuals as reference data;
- five directly collected Factors at different native grains;
- Copy, Average, Maximum, and Minimum strategies;
- one explicit row-level override;
- 36 materialized Contributions in one Sub-Workspace;
- Contribution-level revisions and package withdrawal before approval;
- finalization to 48 facts with retained lineage;
- an independent Node-total verification with zero variance.

The repeated Bridge creation and result-review steps were not rerun because they were unchanged from the already verified operating flow.

## Decide the population policy first

Each directly collected Factor should reference a Population Policy Template that defines the allowed method for the target Version meaning.

A reusable template may contain different rules for different Version meanings. For example:

| Version meaning | Population method |
| --- | --- |
| Actual | Uploaded by contributor |
| Forecast | Package strategy using reference data |

Confirm the complete Version lifecycle before assigning the template. Replacing a Factor's template with a Forecast-only template can accidentally remove the required Actual rule.

## Choose the strategy by business meaning

| Population choice | Business interpretation | Reference requirement |
| --- | --- | --- |
| Copy one prior Version and Period | Carry forward one governed observation | One finalized source |
| Average reference values | Use the arithmetic mean by native coordinate | Multiple finalized sources |
| Maximum reference value | Simulate the highest observed value by native coordinate | Multiple finalized sources |
| Minimum reference value | Simulate the lowest observed value by native coordinate | Multiple finalized sources |
| Median reference value | Use the middle observed value by native coordinate | Multiple finalized sources |
| Prior value plus a percentage | Apply a governed percentage change | One finalized source plus percentage |
| Prior value plus an amount | Apply a governed absolute change | One finalized source plus amount |
| Enter or upload values | Supply direct values instead of deriving defaults | Governed CSV workflow |

!!! note "Statistical strategies work coordinate by coordinate"
    Maximum, Minimum, and Median do not select one complete historical scenario. They select a result independently for each native-grain coordinate. The generated combination may never have existed as one historical package.

### Reference Version and Period

Choose the Reference Version and Reference Period rule deliberately:

- **Immediately previous Period** selects the nearest eligible prior Period.
- **Previous Periods** uses available prior Periods up to the Maximum Period count.
- **Specific Period** selects an explicit eligible Period.
- multiple eligible Versions and Periods may be used by statistical strategies.

Maximum Period count is a ceiling. If only two eligible prior Periods exist when the ceiling is three, CtrlVector uses the two available Periods and reports the reduced window as information. Strategy-specific minimum-source rules still apply.

## End-to-end procedure

### 1. Create and inspect the target Workspace

1. Create the target Analysis, Version, and Period Workspace in `SUBWK`.
2. Open the automatically created Analysis Sub-Workspace or materialize its expected Contributions.
3. Confirm the expected Contribution inventory before creating a package.

The frozen population plan follows the selected Analysis and its required Factor dependencies. It should not contain unrelated tenant Factors.

### 2. Enter SUBST through the Workspace

Select the Version and Period Workspace in `SUBST`. This is the single entry gate for both CSV and reference-based population.

Sub-Workspace, Contribution, and Dimension controls inside Workspace Upload Review are review filters. They do not narrow the eventual Apply scope for the selected Factor. Governed row matching assigns submitted rows to the appropriate materialized Contributions and Sub-Workspaces.

### 3. Start the reference package

Select **Start reference population**. CtrlVector creates a governed zero-row package; a dummy CSV is not required.

Package creation and Factor population are separate checkpoints. Wait for package confirmation before configuring the first Factor strategy.

### 4. Calculate and preview one Factor

For each input Factor:

1. select the Factor;
2. select the Population choice;
3. choose the Reference Version and Reference Period rule;
4. set any strategy parameters;
5. select **Calculate and preview**;
6. inspect every native-grain row before Apply.

The worksheet separates calculated evidence from submission intent:

| Worksheet content | Editability | Meaning |
| --- | --- | --- |
| Dimension Combination | Read-only | Target native-grain business coordinate |
| Reference data | Read-only | Finalized values used by the strategy |
| Calculation | Read-only | Calculation that produced the default |
| Draft submission | Editable | Value proposed for the target package |

Editable worksheet controls use blue text; read-only values remain neutral.

### 5. Confirm or override the Draft

The calculated value initializes Draft submission. You may:

- change one or more Draft values before Apply; or
- select **Use calculated defaults as submitted** when every default is accepted unchanged.

This acknowledgement proves that unchanged defaults were reviewed intentionally. It avoids creating a meaningless numerical override merely to enable Apply.

At Apply, the submitted value is frozen together with:

- reference Version and Period;
- source finalized facts and values;
- calculation expression;
- calculated default;
- submitted value;
- override indicator;
- matched Contribution evidence.

Repeat Apply is blocked for an instruction that has already succeeded.

## Validate behavior constraints before Apply

A mathematically valid statistical result may still violate the Factor's behavior.

For a compositional-share Factor, group Draft rows by every Dimension except the governed composition Dimension and confirm each group equals the configured total, normally `1.0`.

Example: Mode Mix is compositional across Air, Ocean, and Surface for each Country and Product. Maximum values selected independently from several periods may sum to more than `1.0`. Correct the Draft before Apply; do not defer the failure to approval or finalization.

## Retrieve submitted values

Covered Contributions must remain inspectable.

1. Select the applied Factor and relevant Covered Contributions.
2. Open **View submitted values**.
3. Reconcile the read-only **Submitted** column with the calculated default.
4. For overrides, confirm the submitted value and override indicator remain distinct from the reference calculation.

The Submitted worksheet is reconstructed from frozen lineage, not recalculated from current source data.

## Correct values before approval

Use the smallest safe correction boundary.

### Revise one Contribution

Use **Revise selected Contribution** when one owner's Factor scope is wrong:

1. select exactly one Covered, unapproved Contribution;
2. compare Current submission with its Dimension context;
3. enter the Revised submission;
4. submit the numbered revision.

The revision preserves before-and-after values and source lineage. It is blocked when there is no numerical change, when the Contribution is already approved, or when the physical row is shared with another Contribution.

### Withdraw a package

Withdraw the package when the package is broadly invalid.

- **Withdraw** removes the package from active coverage and recalculates unsupported Contributions.
- **Resubmit** restores the identical frozen package.
- **Start replacement package** creates corrected values while retaining the withdrawn package as history.

Withdrawal is blocked after a linked Contribution is approved.

When package names repeat, identify the intended package using its row count, creation time, and short administrative reference. Wait for the blocking progress overlay to finish before checking Contribution coverage or performing another action.

## Complete the governed submission chain

Reference-generated facts follow the same downstream controls as uploaded facts:

```text
SUBST → SUBAP → SUBFN → SUBTV
```

1. In `SUBAP`, confirm the authoritative package covers every expected Contribution, then approve eligible Contributions.
2. In `SUBFN`, finalize the ready Sub-Workspace and confirm the expected record population.
3. In `SUBTV`, calculate Node totals and enter an independently derived Expected total.
4. Record the verification only when Expected and Actual reconcile within the approved tolerance.

## Reconciled example

The verified Freight Spend scenario used:

```text
Freight Spend = HW KUnits × MoT Mix × (KG Per Unit ÷ Fill Rate) × Rate Per KG
```

The target package contained 36 directly populated facts. Finalization added eligible derived facts and produced 48 finalized records. Independent arithmetic across the 12 full-grain business coordinates produced:

```text
Expected total = 8,121.438034188034
Actual total   = 8,121.438034188034
Variance       = 0
Tolerance      = 0
```

The equality demonstrates the control outcome; it does not mean Expected should be copied from Actual. Expected must be calculated independently from the approved business formula and source values.

## Exit evidence

- [ ] Population Policy covers the target Version meaning
- [ ] Expected Contributions are materialized
- [ ] Reference package is uniquely identifiable
- [ ] Every Factor strategy and source window is reviewed
- [ ] Draft overrides are explicit and lineage-bearing
- [ ] Compositional or other behavior checks pass
- [ ] Submitted values can be retrieved read-only
- [ ] Corrections are completed before approval
- [ ] One authoritative package covers all expected Contributions
- [ ] Contributions are approved
- [ ] Finalization produces the expected facts with lineage
- [ ] SUBTV records an independent totals check

## Related material

- [Load and govern data](data-workflow.md)
- [Prepare and submit data](../operating/prepare-and-submit-data.md)
- [Finalize databases](../operating/finalize-databases.md)
- [Population policies and Factors](../configuration/factors.md)
