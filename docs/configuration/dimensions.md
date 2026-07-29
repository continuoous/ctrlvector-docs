# Configure dimensions

!!! success "Page status: Validated"
    Validated against the CtrlVector Dimension Master Workbench and DIM bulk-file contract on 29 July 2026.

## Purpose

Dimensions classify facts so users can analyze change by business perspectives such as entity, region, product, channel, customer, or cost center. A governed dimension gives every member a stable business value and, when required, a place in a hierarchy.

## Learning objectives

After completing this chapter, you should be able to:

- explain the difference between a dimension definition and a dimension member;
- choose the correct DIM transaction for a task;
- create a dimension and maintain its hierarchy;
- inspect a hierarchy using Outline, Paths, and Table views;
- prepare and validate normalized DIM09 files;
- verify saved results and identify common hierarchy errors.

## Prerequisites

Before configuration, agree the following with the business owner:

- canonical and display names for the dimension;
- the business scope and intended analytical use;
- canonical values for its members;
- required parent-child relationships;
- member display order and active status;
- the person responsible for ongoing maintenance.

You also need authorization for the relevant DIM transaction.

## Business objects and transaction family

A **dimension definition** describes the classification as a whole. A **dimension member** is one governed value inside it. For example, `GEOGRAPHY` is a dimension; `Europe` and `Poland` are members.

| Transaction | Intention | Permitted work |
|---|---|---|
| `DIM01` | Create | Create a dimension definition, then continue maintenance in Change mode. |
| `DIM02` | Change | Change the definition; add, edit, move, activate, deactivate, or remove members. |
| `DIM03` | Display | Inspect a definition and hierarchy without mutation. |
| `DIM09` | Import | Download templates or exports, upload files, validate a staged preview, and commit bulk changes. |

The four transactions are different modes of one governed business object. Display mode cannot save changes.

## Example hierarchy

This chapter uses a geography dimension:

```text
Global
├── Europe
│   ├── Poland
│   └── Germany
└── Americas
    ├── United States
    └── Canada
```

| Member | Parent | Sort order | Status |
|---|---|---:|---|
| Global | — | 10 | Active |
| Europe | Global | 20 | Active |
| Poland | Europe | 30 | Active |
| Germany | Europe | 40 | Active |
| Americas | Global | 50 | Active |
| United States | Americas | 60 | Active |
| Canada | Americas | 70 | Active |

`sort_order` controls presentation. It does not replace the stable member identity shown by CtrlVector.

## Create a dimension with DIM01

1. Open `DIM01`.
2. Enter a unique **Canonical name**, such as `GEOGRAPHY`.
3. Enter the business-facing **Display name** and a clear **Description**.
4. Confirm that the definition should be **Active**.
5. Save the page.
6. Continue in `DIM02` for member and hierarchy maintenance.

### Expected result

CtrlVector confirms creation, selects the new definition, and opens it for change. The saved definition becomes available to the other DIM transactions.

## Build and change the hierarchy with DIM02

1. Open `DIM02` and select the dimension object.
2. Review the definition before changing member data.
3. Add the root member first. Leave its parent empty.
4. Add each child with its canonical value, parent, and sort order.
5. Use **Outline** to review structure or drag a member onto a new parent.
6. Use **Table** to maintain member fields in a worksheet representation.
7. Stage status changes or removals as required.
8. Save the page.
9. Confirm staged removals if CtrlVector asks for confirmation.

A successful save returns to `DIM03`, where you can verify the persisted result. CtrlVector prevents a member from being moved beneath itself or one of its descendants.

### Unsaved changes and shortcuts

| Command | Result |
|---|---|
| `Ctrl+S` or `Cmd+S` | Save the definition, member draft, member edits, hierarchy moves, and staged removals. |
| `Esc` | Return to `DIM03`; CtrlVector requests confirmation when unsaved changes exist. |
| `F3` | Return to the home transaction. |

## Inspect a dimension with DIM03

Select the dimension, then choose the view that matches your question:

| View | Use it to answer |
|---|---|
| **Outline** | Where does each member sit in the hierarchy? |
| **Paths** | What is the complete root-to-leaf business path? |
| **Table** | Which attributes, parents, orders, and statuses are assigned? |

Search includes canonical values, parents, paths, and stable member index values. Type and status filters can narrow the hierarchy. Contextual results retain ancestors so a matching member remains understandable.

Useful display commands include:

| Command | Result |
|---|---|
| `F2` | Open the selected dimension in `DIM02`. |
| `Ctrl+F` or `Cmd+F` | Focus hierarchy search. |
| `Alt+1` / `Alt+2` / `Alt+3` | Select Outline, Paths, or Table. |
| `Esc` | Clear an active hierarchy search. |

## Maintain dimensions in bulk with DIM09

DIM09 separates definition-grain and member-grain data:

| Data object | Grain | File type |
|---|---|---|
| `MDDIM` | One dimension definition per row | CSV |
| `MDMEM` | One member within one dimension per row | CSV |

### File columns

`MDDIM`:

```text
dimension_canonical_name,dimension_display_name,
dimension_description,dimension_is_active
```

`MDMEM`:

```text
dimension_canonical_name,member_canonical_value,
parent_canonical_value,member_sort_order,member_is_active
```

### Bulk procedure

1. Choose **Template** for a construction example, or **Export** for current round-trippable data.
2. Maintain definitions in `MDDIM` and members in `MDMEM`.
3. Refer to dimensions and parents by canonical business value, not database ID.
4. Choose **Upload** for each required file.
5. Review the staged preview and resolve every validation error.
6. Choose **Commit** only after the complete preview is valid.
7. Open `DIM03` and verify the resulting definitions and paths.

Upload only stages data; it does not persist changes. Template and Export serve different purposes, so do not treat a small template as a backup of saved data.

## Worked example

Create `GEOGRAPHY` in `DIM01`, then add the seven example members in `DIM02`.

To test hierarchy maintenance:

1. Select `Poland` in Outline.
2. Confirm that its path is `Global > Europe > Poland`.
3. In `DIM02`, move `Poland` to `Global` and save.
4. In `DIM03`, verify that its path is now `Global > Poland`.
5. Return to `DIM02`, restore `Europe` as the parent, and save.
6. Verify the original path again.

This exercise demonstrates that the parent relationship governs the path while sort order governs presentation among peers.

## Verification checklist

- The dimension canonical name is unique and meaningful.
- Display name and description communicate the business purpose.
- Every member has a unique canonical value within the dimension.
- Every non-root member has a valid parent.
- No hierarchy cycle exists.
- Sort order produces the intended outline.
- Inactive members are intentional.
- Outline, Paths, and Table present consistent results.
- `DIM03` shows the expected persisted hierarchy after every save or bulk commit.

## Common errors

| Symptom | Likely cause | Resolution |
|---|---|---|
| A member appears at the root unexpectedly | Its parent is empty or cannot be resolved. | Assign the intended active parent and save again. |
| A move is rejected | The target would create a hierarchy cycle. | Choose a parent outside the member's descendant branch. |
| Siblings appear in the wrong sequence | Their sort orders do not match the intended presentation. | Correct sort order in `DIM02` or `MDMEM`. |
| Commit is unavailable in `DIM09` | No completely valid staged preview exists. | Upload the required file data and resolve every validation error. |
| A bulk parent does not resolve | The parent value is not a canonical member value in the same dimension. | Correct `parent_canonical_value` or add the missing parent. |
| Changes seem to disappear | The page was exited without saving or the staged bulk data was not committed. | Save with `Ctrl+S`, or Commit the validated DIM09 preview. |

## Practice

Design a `PRODUCT` dimension with one root, at least two product families, and at least two products below each family.

1. Record the intended canonical values, parents, sort order, and status.
2. Create the dimension and members.
3. Verify one leaf in each of the three hierarchy views.
4. Move one product to a different family, verify the new path, and then restore it.
5. Export the current DIM data and identify which rows belong to `MDDIM` and `MDMEM`.

## Knowledge check

1. What is the difference between a dimension definition and a dimension member?
2. Why does `DIM03` have no save behavior?
3. Which view shows only complete root-to-leaf paths?
4. What is the difference between member parent and member sort order?
5. Why does DIM09 use separate `MDDIM` and `MDMEM` files?
6. At what point does uploaded DIM09 data become persistent?

??? example "Answers"
    1. The definition describes the governed classification; a member is one value within it.
    2. `DIM03` represents Display intention and is read-only.
    3. Paths.
    4. Parent controls hierarchy placement; sort order controls presentation among members.
    5. Definitions and members have different business grains, so definition attributes should not repeat on every member row.
    6. Only when the user commits a completely valid staged preview.

## Related material

- [Core business objects](../concepts/core-business-objects.md)
- [Dimension drilldowns](../analysis/dimension-drilldowns.md)
- [Transaction-code reference](../reference/transaction-codes.md)
- [File and field reference](../reference/file-and-field-reference.md)

## Product applicability

This chapter applies to the CtrlVector Dimension Master Workbench and DIM bulk-task behavior validated on 29 July 2026. Revalidate it when the DIM interaction, keyboard contract, or `MDDIM`/`MDMEM` file schema changes.
