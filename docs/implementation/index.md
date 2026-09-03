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

The journey uses a strategy-led design method. Begin with the decisions leaders need to make, establish independent outcome totals, define the reusable calculation contract, and then work backward to the minimum sufficient Factors, Dimensions, grains, evidence, owners, and controls. Detailed data is the bottom-up proof of that approved contract—not the starting point for deciding what the model should explain.

Treat documentation as part of implementation, not as a workshop-close activity. After each material design decision or test, update the relevant guide section and decision trail while preserving the original approach, the evidence that triggered a pivot, the confirmed replacement, and its validation consequences. This gives later implementers and agents the reasoning needed to distinguish intentional design from accidental configuration.

## Journey map

| Stage | Outcome | Exit evidence |
| --- | --- | --- |
| 1. [Plan](plan.md) | Agreed business question, grains, governance, and controls | Signed design workbook and acceptance plan |
| 2. [Build the foundation](foundation.md) | Dimensions and time objects exist | Display transactions confirm saved objects and members |
| 3. [Configure the semantic model](semantic-model.md) | Policies, Factors, derivations, Node, and Analysis align | Effective Analysis scope contains the intended analytical and supporting Factors |
| 4. [Load and govern data](data-workflow.md) | Uploaded or [reference-populated](reference-population.md) inputs are submitted at native grain | Contribution coverage, Draft values, and lineage reconcile |
| 5. [Govern submissions and approve the Workspace](workspace-approval.md) | Contributor evidence becomes one controlled Workspace decision | Baseline/result reconciliation, issue resolution, approval, finalization, and totals controls reconcile |
| 6. [Create and validate the Bridge](bridge-validation.md) | The comparison is explainable and auditable | Independent totals, Shapley results, filtered detail, and lineage reconcile |

## Verified applicability

The workshop behind this Draft used:

- one development tenant and an unrestricted implementation role;
- fiscal Quarter periods and an Actuals version;
- three business dimensions;
- directly entered and derived Factors at different native grains;
- two period-specific submission workspaces;
- a Quarter-over-Quarter Bridge reviewed in BRRES.

A second workshop reused the same semantic model for a Forecast Version. It verified governed Copy and statistical reference strategies, Draft overrides, Contribution revisions, package reconciliation, approval, lineage-bearing finalization, and independent totals verification through `SUBTV`. The unchanged Bridge steps were not repeated.

A third workshop tested the submission-to-approval control boundary with a Scenario Version. It verified submission-baseline impact, structured contributor evidence, targeted revision, candidate recalculation, policy-issue resolution, and one holistic Workspace approval. Finalization and totals verification were outside that workshop's scope.

A fourth workshop verified one Scenario Workspace containing two Analysis Sub-Workspaces end to end. It exercised shared and Analysis-local submission evidence, separate financial and operational policy observations, targeted Contribution revision, candidate recalculation, one Workspace approval, separate immutable Analysis artifacts, idempotent finalization retry, Node-level totals verification, separate Bridges for each Analysis, governed narrative lineage at Factor scope, and an explicit no-movement Bridge result.

## Important boundary

This guide does not yet define production tenant provisioning, authentication, multi-user persistence, hosting, backup, or concurrency architecture. Treat those as deployment-design decisions, not as completed CtrlVector procedures.

RUNCR and RUNSB are also not available as frontend procedures in the current documented workflow. The verified path proceeds from finalized submissions to BRDGE.

## Before continuing

Read [Product overview](../getting-started/product-overview.md) and [Core business objects](../concepts/core-business-objects.md). Keep the [Transaction-code reference](../reference/transaction-codes.md) open as you work.
