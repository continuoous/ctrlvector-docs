# Configure factors

!!! warning "Page status: Draft"
    This chapter includes current FAC02 derivation and behavior guidance. It requires product-owner and learner-clarity review before validation.

## Chapter purpose

Explain how measurable business drivers are governed and made available to node calculations.

## Chapter coverage

- factor identity, meaning, behavior, and status;
- source and derived factors;
- dimension and population policies;
- collation and compatibility;
- create, change, display, and bulk-maintenance tasks;
- worked example, verification, common errors, and practice.

## Prerequisite

Complete [Dimensions](dimensions.md), review [Dimension Policy Templates](dimension-policy-templates.md), and read [Core business objects](../concepts/core-business-objects.md).

## FAC02: derivation and behavior are different

FAC02 separates two questions that are easy to confuse:

| Configuration | Question answered | Ratio example |
| --- | --- | --- |
| **Factor derivation** | How is the factor calculated? | `Gross Margin % = Gross Profit ÷ Revenue` |
| **Factor behavior** | How must that value respond when CtrlVector changes grain? | Recompute as `Σ Gross Profit ÷ Σ Revenue` |

The division formula belongs under **Factor derivation**. Select the **Ratio** operation, then identify its numerator and denominator factors. Under **Factor behavior**, select **Recomputed ratio**. FAC02 displays the linked formula there but does not ask for the same factor references again.

### Why a derived ratio is not invariant

A derived factor is not automatically an invariant factor. Consider this example:

| Product | Gross Profit | Revenue | Gross Margin % |
| --- | ---: | ---: | ---: |
| A | 20 | 100 | 20% |
| B | 30 | 300 | 10% |
| **Total** | **50** | **400** | **12.5%** |

The total is `50 ÷ 400 = 12.5%`. Retaining either product's percentage would be wrong, and taking the simple average would produce `15%`, which is also wrong. A recomputed ratio therefore aggregates its source measures first and divides second:

`Σ numerator ÷ Σ denominator`

Use **Invariant value** only when a value is genuinely shared unchanged across the relevant members, such as a governed assumption that is intentionally repeated. The fact that a value was derived does not make it invariant.

## Product derivations

Use the **Product** derivation when multiplication creates a reusable business factor. FAC02 asks for exactly two sources:

- **Multiplicand factor** — the first factor in `X × Y`;
- **Multiplier factor** — the second factor in `X × Y`.

For example, Revenue may be defined as `Unit Price × Units Sold`. Keeping Revenue as a factor makes it available for reuse across nodes and analyses. A node remains the better place for an analysis-specific relationship, such as explaining the Revenue change through price and volume effects.

The derived factor's behavior remains an independent business decision. A product is not automatically additive or invariant. For example, `Unit Price × Units Sold` normally produces an additive amount, while another product may require different governance.

### Building an auditable weighted average

A Product derivation is especially useful when the weighted numerator should be visible or reusable:

| Product | Rate | Units (weight) | Rate × Units |
| --- | ---: | ---: | ---: |
| A | 10 | 20 | 200 |
| B | 15 | 80 | 1,200 |
| **Total** | — | **100** | **1,400** |

Configure two derived factors:

1. **Weighted numerator** — Product derivation: `Rate × Units`; normally Additive behavior.
2. **Weighted average rate** — Ratio derivation: `Weighted numerator ÷ Units`; Recomputed ratio behavior.

At the total grain, CtrlVector recomputes the weighted average as:

`Σ(Rate × Units) ÷ Σ Units = 1,400 ÷ 100 = 14`

Use **Weighted rate** behavior instead when the weighted numerator is only an implementation detail and does not need to be exposed as its own reusable factor.

!!! note "Product validation"
    A source may either have the target's native grain or be a strict subset of it. A coarser source is accepted only when its Dimension Policy Template explicitly configures every missing target dimension as **Missing/expansion target** with `broadcast`. Their formats do not need to be identical: for example, `USD/unit × units` may produce a target formatted as `USD`. Configure the target format explicitly in FAC02.

### Derivations with governed broadcast

Derivation finalization can align a coarser source to a finer target before applying the configured operation. This is useful when a quantity is supplied at one business grain and another source is available at additional dimensions. Product provides the clearest example.

Suppose HW KUnits is supplied at Country + Product grain:

| Country | Product | HW KUnits |
| --- | --- | ---: |
| Canada | Laptops | 30 |

MoT Mix is supplied at Country + Product + Mode of Transport grain:

| Country | Product | Mode of Transport | MoT Mix |
| --- | --- | --- | ---: |
| Canada | Laptops | Air | 50% |
| Canada | Laptops | Ocean | 30% |
| Canada | Laptops | Surface | 20% |

Configure HW KUnits as Semi-additive, with Country and Product native and Mode of Transport set to missing/`broadcast`. Configure MoT Mix and the derived XFactory at the full three-dimension grain. Then define:

`XFactory = HW KUnits × MoT Mix`

During finalized-database preparation, CtrlVector repeats the governed HW KUnits value across the active leaf members of Mode of Transport and joins both sources at the XFactory grain:

| Mode of Transport | Calculation | XFactory |
| --- | ---: | ---: |
| Air | `30 × 50%` | 15 |
| Ocean | `30 × 30%` | 9 |
| Surface | `30 × 20%` | 6 |
| **Total** |  | **30** |

This is controlled expansion, not duplicated input. The finalized derived rows retain lineage to the original source rows and identify which dimension was broadcast.

The same governed alignment applies when another derivation operation has a coarser source. For example, if KG Per Unit is supplied by Product while Fill Rate is supplied by Country + Product + Mode, `Weight = KG Per Unit ÷ Fill Rate` broadcasts KG Per Unit across its explicitly governed missing Country and Mode dimensions before division. Each operation still keeps its own rules: Ratio divides numerator by denominator, Sum and Difference combine aligned coordinates, Copy repeats one governed source, and Project Sum remains the separate operation for removing sum-governed dimensions.

The same factor policies can also govern a multi-factor PR Node. Suppose Freight Spend is defined as:

`HW KUnits × MoT Mix × Weight × Rate Per KG`

HW KUnits may be native to Country + Product and broadcast to Mode, while Rate Per KG may be native to Country + Mode and broadcast to Product. If MoT Mix and Weight are already native to all three dimensions, Bridge preparation aligns all four factors to Country + Product + Mode before decomposition. BRRES can then attribute the change to the four factors and their interactions without requiring duplicate input rows.

Using the derived Weight factor deliberately combines the effects of its own sources. If `Weight = KG Per Unit ÷ Fill Rate`, the Freight Spend Node exposes one Weight effect rather than separate KG Per Unit and Fill Rate effects.

!!! failure "When finalization stops"
    CtrlVector rejects the derivation if a missing target dimension is not explicitly governed as `broadcast`. Finalization also stops for missing source facts, duplicate source coordinates, an empty active-leaf expansion domain, a zero Ratio denominator, or an unresolved derivation chain. It does not silently invent a split or choose a member.

### Ratio configuration sequence

For a factor such as Gross Margin %:

1. Create Gross Profit, Revenue, and Gross Margin % as factors.
2. Assign compatible native dimension grains to the three factors.
3. In **Factor derivation**, select Gross Margin % as the target.
4. Select **Ratio** and configure Gross Profit as the numerator and Revenue as the denominator.
5. Save the derivation.
6. In **Factor behavior**, select **Recomputed ratio** for Gross Margin % and save.
7. Govern rollup and expansion through the assigned dimension policy template.

!!! warning "Configure the derivation first"
    Recomputed ratio behavior requires a saved Ratio derivation. FAC02 prevents the behavior from being saved until both numerator and denominator have been configured.
