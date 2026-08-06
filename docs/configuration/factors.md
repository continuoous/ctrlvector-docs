# Configure factors

!!! info "Page status: Planned"
    Factor behavior, policy, and maintenance procedures still require validation.

## Chapter purpose

Explain how measurable business drivers are governed and made available to node calculations.

## Planned coverage

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
    The two source factors and the target must have compatible native dimension grains. Their formats do not need to be identical: for example, `USD/unit × units` may produce a target formatted as `USD`. Configure the target format explicitly in FAC02.

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
