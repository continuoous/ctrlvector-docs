# Configure dimensions

## Purpose

Dimensions classify facts so users can analyze variance by perspectives such as entity, region, product, channel, customer, or cost center.

## Transaction family

| Transaction | Purpose |
|---|---|
| DIM01 | Create a dimension and stage new members. |
| DIM02 | Change an existing dimension or hierarchy. |
| DIM03 | Display a dimension without changing it. |
| DIM09 | Import or mass-maintain dimensions and members. |

## Prerequisites

Before configuration, decide the canonical name, business scope, member values, hierarchy structure, parent relationships, and maintenance owner.

## Example hierarchy

A geography hierarchy can be represented as:

    Global
    ├── Europe
    │   ├── Poland
    │   └── Germany
    └── Americas
        ├── United States
        └── Canada

| Member | Parent | Order | Status |
|---|---|---:|---|
| Global | — | 10 | Active |
| Europe | Global | 20 | Active |
| Poland | Europe | 30 | Active |
| Germany | Europe | 40 | Active |
| Americas | Global | 50 | Active |
| United States | Americas | 60 | Active |
| Canada | Americas | 70 | Active |

## Create with DIM01

1. Open DIM01.
2. Enter the canonical name and display information.
3. Stage the root and child members.
4. Assign the appropriate parent to every non-root member.
5. Review the outline and save.
6. Reopen the dimension in DIM03 and verify the hierarchy.

## Review with DIM03

Use the outline view to expand branches, inspect parent relationships, confirm order and status, and search for members or paths. DIM03 is read-only.

## Change with DIM02

Locate the member, correct its governed attributes or parent, review pending changes, save, and verify again in DIM03. A member cannot be moved beneath one of its descendants.

## Bulk maintenance with DIM09

Download the template or export, populate rows, upload the file, correct validation errors, review the staged preview, commit, and verify the result in DIM03.

!!! note "Pilot chapter"
    Detailed CSV columns and screenshot-led procedures will be added after validation against the current product release.

## Verification checklist

- The dimension name is unique and meaningful.
- Every non-root member has a valid parent.
- No hierarchy cycle exists.
- Member order produces the intended outline.
- Inactive members are intentional.
- DIM03 displays the expected hierarchy.
