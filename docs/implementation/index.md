# CtrlVector implementation guide

!!! warning "Page status: Draft"
    This guide is based on a completed development-environment workshop. It requires product-owner and implementation-lead review before validation.

## Purpose

Use this guide to move from a business comparison question to a reconciled CtrlVector Bridge without creating downstream objects before their prerequisites are understood.

This is an implementation journey, not a field-by-field reference. Each chapter tells you:

- what must be decided before the stage begins;
- which CtrlVector objects are created;
- what evidence proves the stage is complete;
- which detailed configuration chapter to use when you need field guidance.

## Journey map

| Stage | Outcome | Exit evidence |
| --- | --- | --- |
| 1. [Plan](plan.md) | Agreed business question, grains, governance, and controls | Signed design workbook and acceptance plan |
| 2. [Build the foundation](foundation.md) | Dimensions and time objects exist | Display transactions confirm saved objects and members |
| 3. [Configure the semantic model](semantic-model.md) | Policies, Factors, derivations, Node, and Analysis align | Effective Analysis scope contains the intended analytical and supporting Factors |
| 4. [Load and govern data](data-workflow.md) | Reference and current inputs are finalized and verified | Contribution, approval, finalization, and totals controls reconcile |
| 5. [Create and validate the Bridge](bridge-validation.md) | The comparison is explainable and auditable | Independent totals, Shapley results, filtered detail, and lineage reconcile |

## Verified applicability

The workshop behind this Draft used:

- one development tenant and an unrestricted implementation role;
- fiscal Quarter periods and an Actuals version;
- three business dimensions;
- directly entered and derived Factors at different native grains;
- two period-specific submission workspaces;
- a Quarter-over-Quarter Bridge reviewed in BRRES.

## Important boundary

This guide does not yet define production tenant provisioning, authentication, multi-user persistence, hosting, backup, or concurrency architecture. Treat those as deployment-design decisions, not as completed CtrlVector procedures.

RUNCR and RUNSB are also not available as frontend procedures in the current documented workflow. The verified path proceeds from finalized submissions to BRDGE.

## Before continuing

Read [Product overview](../getting-started/product-overview.md) and [Core business objects](../concepts/core-business-objects.md). Keep the [Transaction-code reference](../reference/transaction-codes.md) open as you work.
