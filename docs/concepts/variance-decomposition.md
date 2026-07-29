# Exact variance decomposition

!!! warning "Page status: Draft"
    The numerical example reconciles, but the chapter still requires an independent instructional review.

## Learning objectives

After completing this chapter, you should be able to calculate a simple two-factor variance, distinguish pure and interaction effects, and verify exact reconciliation.

## Worked example: revenue

Assume revenue equals price multiplied by volume.

| Measure | Baseline | Current | Change |
|---|---:|---:|---:|
| Price | 100 | 110 | 10 |
| Volume | 1,000 | 1,200 | 200 |
| Revenue | 100,000 | 132,000 | 32,000 |

### Total variance

Current revenue minus baseline revenue is 132,000 minus 100,000, or **32,000**.

### Pure price effect

Hold volume at baseline: (110 - 100) × 1,000 = **10,000**.

### Pure volume effect

Hold price at baseline: 100 × (1,200 - 1,000) = **20,000**.

### Interaction effect

Both factors changed: (110 - 100) × (1,200 - 1,000) = **2,000**.

### Reconciliation

10,000 + 20,000 + 2,000 = **32,000**. The explanation reconciles exactly to the total revenue movement.

!!! warning "Relationship-specific example"
    The correct calculation depends on the configured node type and factor relationship. This example teaches a two-factor product relationship only.

## Practice exercise

A product has a baseline unit cost of 8 and volume of 500. Current unit cost is 9 and volume is 550. Calculate baseline cost, current cost, both pure effects, the interaction, and the reconciliation.

??? example "Solution"
    Baseline cost is 4,000. Current cost is 4,950. The unit-cost effect is 500, the volume effect is 400, and the interaction is 50. The effects total 950.
