# Product overview

!!! warning "Page status: Draft"
    The product framing is ready for review; interface and release-specific claims still require validation.

## Learning objectives

After completing this chapter, you should be able to:

- describe the business problem CtrlVector solves;
- distinguish configuration, submitted data, finalized data, bridges, and results;
- identify the major stages of the operating workflow.

## Why CtrlVector exists

A financial result can move for several reasons at the same time. Revenue, cost, margin, and operational KPIs commonly depend on price, volume, rate, mix, productivity, and other drivers.

A conventional report can show that a value changed, but it may not explain the movement consistently. CtrlVector represents the metric explicitly and decomposes its movement into additive, auditable impacts.

> Every reported impact must reconcile to the total change and remain traceable to governed configuration and submitted source data.

## Core business objects

| Object | Purpose |
|---|---|
| Dimension | Defines a business classification such as region, product, or channel. |
| Factor | Defines a measurable business driver such as units, price, or rate. |
| Node | Defines how factors combine into a business result. |
| Version | Identifies business meaning such as Actual, Budget, or Forecast. |
| Calendar and period | Define the governed time axis used for comparisons. |
| Analysis | Instantiates reusable configuration for an analytical purpose. |
| Finalized database | Freezes the accepted canonical input facts. |
| Bridge | Compares two compatible finalized databases. |

## End-to-end workflow

1. Establish environment master data.
2. Define or instantiate the analysis configuration.
3. Collect and validate data for a version and period.
4. Finalize the canonical input database.
5. Create a bridge between compatible baseline and current databases.
6. Review reconciled impacts and drill into dimensions.
7. Apply governed decision and ownership structures where configured.

## Knowledge check

1. What makes a CtrlVector explanation auditable?
2. Why are versions and periods governed objects?
3. What is frozen by a finalized database?
4. Which object compares baseline and current databases?
