# Configure dimensions

!!! warning "Page status: Draft"
    Revised against the current CtrlVector Dimension Master Workbench and DIM bulk-file contract on 30 July 2026. Product-owner and learner-clarity review is required before returning this page to Validated.

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

Member canonical values are case-insensitively unique **within one dimension**, not globally. Two dimensions may both contain `Georgia`, for example. The combination of dimension and member supplies the business context. CtrlVector preserves and displays the casing entered for the saved canonical value; it does not silently normalize `Poland` to another casing. A case variant such as `POLAND` is treated as a duplicate, not coalesced with `Poland`. In DIM09, that duplicate rejects the atomic package.

| Transaction | Intention | Permitted work |
|---|---|---|
| `DIM01` | Create | Create a dimension definition, then continue maintenance in Change mode. |
| `DIM02` | Change | Change the definition; add, edit, move, activate, deactivate, or remove members. |
| `DIM03` | Display | Inspect a definition and hierarchy without mutation. |
| `DIM09` | Import | Create one or more new dimensions and their initial member hierarchies from normalized bulk files. |

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
| Europe | Global | 10 | Active |
| Poland | Europe | 10 | Active |
| Germany | Europe | 20 | Active |
| Americas | Global | 20 | Active |
| United States | Americas | 10 | Active |
| Canada | Americas | 20 | Active |

`sort_order` is compared among members that share the same parent. It does not need to be unique across the dimension, which is why separate branches reuse `10` and `20` above. If siblings have the same order, canonical value breaks the display tie. Sort order does not replace the stable member identity shown by CtrlVector.

### Stable IDX segment

When a member is first saved, CtrlVector assigns an immutable positive **member IDX** within that dimension. DIM03 displays it as a four-digit **IDX segment**, such as `0003`, in Outline, Paths, Table, and the selected-member inspector.

The IDX segment is not:

- the member's sort order;
- its hierarchy level;
- its parent relationship; or
- a promise of business chronology.

Renaming, moving, reordering, activating, or deactivating a member does not change its IDX segment. Finalized fact identifiers use these stable member segments, so search includes them for reconciliation.

The fact-row `idx` discussed under Snapshot `select` in [Dimension Policy Templates](dimension-policy-templates.md#select) is a complete calculation identifier assembled from dimension-member segments. It is not one member's IDX segment. Because member IDX reflects stable identity rather than calendar meaning, it must not be treated as chronological ordering.

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
3. Add a root member first. Leave its parent empty.
4. Add each child with its canonical value, parent, and sort order.
5. Use **Outline** to review structure or drag a member onto a new parent.
6. Use **Table** to maintain member fields in a worksheet representation.
7. Stage status changes or removals as required.
8. Save the page.
9. If any removal is staged, review the consolidated confirmation and choose **Confirm removal and save**.

A successful save returns to `DIM03`, where you can verify the persisted result. CtrlVector prevents a member from being moved beneath itself or one of its descendants.

Multiple parentless root members are valid. Outline displays each root and its tree, so one dimension may form a forest. Roots are siblings for presentation: their sort orders control tree sequence, with canonical value breaking a tie. Use a single root only when the business hierarchy is intended to converge on one top-level member.

### Deactivate or remove a member

Deactivation and removal have different effects:

| Action | Persisted result | Effect on children |
|---|---|---|
| **Deactivate** | The member remains stored with the same IDX, parent, order, and hierarchy position. It remains visible with **All** or **Inactive** status filtering. | Child statuses and parent relationships do not change automatically. |
| **Remove** | The member record and its IDX identity are deleted after the confirmed Save. | Each direct child is promoted to the removed member's parent. If the removed member was a root, its direct children become roots. |

Inactive members are excluded from current active-member maps used by configuration and data workflows. Removal is more disruptive: the current delete operation does not perform a downstream-usage check before deleting the member. Review submissions, mappings, analyses, and other consumers before either action.

A removal is never immediate in DIM02. Selecting Remove stages it and provides a row-level Undo for that removal. Other staged moves, edits, and status changes do not have the same row-level Undo action; edit them back manually or use `Esc` to discard the complete unsaved change set. Saving with one or more staged removals always opens one consolidated confirmation. Having children does not block removal because the promotion rule preserves their branch.

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

Search includes canonical values, parents, paths, and the four-digit IDX segments defined above. Type and status filters can narrow the hierarchy. Contextual results retain ancestors so a matching member remains understandable.

Useful display commands include:

| Command | Result |
|---|---|
| `F2` | Open the selected dimension in `DIM02`. |
| `Ctrl+F` or `Cmd+F` | Focus hierarchy search. |
| `Alt+1` / `Alt+2` / `Alt+3` | Select Outline, Paths, or Table. |
| `Esc` | Clear an active hierarchy search. |

## Maintain dimensions in bulk with DIM09

The current DIM09 workflow creates new dimensions and their initial hierarchies. It does not update, replace, deactivate, or remove members from an existing dimension. Use DIM02 for those changes.

!!! note "Different from DPT09"
    DPT09 can update an existing template whose name matches an uploaded header. Current DIM09 rejects a dimension canonical name that already exists. No future DIM09 upsert behavior is committed in this Draft, so do not design a maintenance process that assumes it will be added.

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

### Current commit semantics

- Both MDDIM and MDMEM files are required for a committable preview.
- Every MDMEM row must reference a dimension present in the uploaded MDDIM file.
- Every non-empty parent must be another member of the same uploaded dimension.
- An uploaded dimension canonical name that already exists in the environment rejects the package; DIM09 does not merge with it.
- The submitted MDMEM rows establish the initial member set for each new dimension. Omitting a member means it is not created; it does not remove a member from an existing dimension.
- The package is atomic: an invalid definition, duplicate, or unresolved parent prevents all submitted dimensions and members from being written.

### Bulk procedure

1. Choose **Template** for a construction example, or **Export** to inspect or seed configuration for another environment.
2. Define new, non-existing dimensions in `MDDIM` and their initial members in `MDMEM`.
3. Refer to dimensions and parents by canonical business value, not database ID. Parent lookup is scoped to the dimension named on the MDMEM row.
4. Choose **Upload** for each required file.
5. Review the staged preview and resolve every validation error.
6. Choose **Commit** only after the complete preview is valid.
7. Open `DIM03` and verify the resulting definitions and paths.

Upload only stages data; it does not persist changes. Export is not round-trippable into the same environment under existing canonical names because current DIM09 is create-only. Template and Export serve different purposes, so do not treat a small template as a backup of saved data.

## Downstream consumers

Dimension Policy Templates reference a **dimension definition**, not individual members. Renaming, deactivating, or removing one member therefore does not automatically change or delete a DPT policy line.

Member-level consumers can still be affected. Inactive members are omitted from active-member maps, while a removed member can no longer resolve through the registry. Before changing status or removing a member:

1. identify submissions, mappings, analyses, and Bridge inputs that use its canonical value;
2. decide how new data should map after the change;
3. preserve any evidence required to interpret historical results;
4. verify affected workflows after Save.

See [Dimension Policy Templates](dimension-policy-templates.md) for dimension-level rollup and expansion governance.

## Worked example

Create `GEOGRAPHY` in `DIM01`, then add the seven example members in `DIM02`.

To test hierarchy maintenance:

1. Select `Poland` in Outline.
2. Confirm that its path is `Global > Europe > Poland`.
3. In `DIM02`, move `Poland` to `Global` and save.
4. In `DIM03`, verify that its path is now `Global > Poland`.
5. Return to `DIM02`, restore `Europe` as the parent, and save.
6. Verify the original path again.
7. In `DIM02`, set Poland's sort order to `20` and Germany's to `10`, then save.
8. In `DIM03`, verify that Germany now appears before Poland in Outline while both Paths remain unchanged.
9. Restore Poland to `10` and Germany to `20`, save, and verify the original sibling order.

This exercise demonstrates that parent governs path while sort order governs presentation among siblings. Reordering does not change either member's IDX segment.

## Verification checklist

- The dimension canonical name is unique and meaningful.
- Display name and description communicate the business purpose.
- Every member has a unique canonical value within the dimension.
- Every non-root member has a valid parent.
- Multiple roots, if present, are intentional.
- No hierarchy cycle exists.
- Sort order produces the intended sequence within each sibling group.
- Inactive members are intentional.
- Every removal has a reviewed child-promotion and downstream-consumer impact.
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
| DIM09 reports that a dimension already exists | Current DIM09 creates new definitions; it does not upsert existing ones. | Use DIM02 for maintenance, or upload a genuinely new canonical dimension name. |
| Removing a branch changes its children's paths | Direct children are promoted to the removed member's parent. | Review the confirmation, then verify every promoted path in DIM03. |
| An inactive member is absent from a selection or mapping list | Active-member maps exclude inactive members. | Reactivate it if still operational, or update the consuming configuration deliberately. |
| IDX does not change after reordering | IDX is immutable identity; it is not presentation order. | Verify the Order column rather than expecting the IDX segment to change. |
| Changes seem to disappear | The page was exited without saving or the staged bulk data was not committed. | Save with `Ctrl+S`, or Commit the validated DIM09 preview. |

## Practice

Design a `PRODUCT` dimension with one root, at least two product families, and at least two products below each family.

1. Record the intended canonical values, parents, sort order, and status.
2. Create the dimension and members.
3. Verify one leaf in each of the three hierarchy views.
4. Move one product to a different family, verify the new path, and then restore it.
5. Swap two sibling products' sort orders and verify that their paths and IDX segments do not change.
6. Export the current DIM data and identify which rows belong to `MDDIM` and `MDMEM`.
7. Explain why that export cannot update the same dimension through current DIM09.

## Knowledge check

1. What is the difference between a dimension definition and a dimension member?
2. Why does `DIM03` have no save behavior?
3. Which view shows only complete root-to-leaf paths?
4. What is the difference between member parent and member sort order?
5. Why does DIM09 use separate `MDDIM` and `MDMEM` files?
6. At what point does uploaded DIM09 data become persistent?
7. Can two different dimensions both contain the member canonical value `Georgia`?
8. What is the difference between sort order and IDX segment?
9. What happens to direct children when their parent is removed?
10. Can DIM09 add members to an existing dimension?
11. If two uploaded dimensions contain a member called `Georgia`, how does DIM09 resolve each row's `parent_canonical_value`?

Select **Answers** to reveal the response key.

??? example "Answers"
    1. The definition describes the governed classification; a member is one value within it.
    2. `DIM03` represents Display intention and is read-only.
    3. Paths.
    4. Parent controls hierarchy placement; sort order controls presentation among members.
    5. Definitions and members have different business grains, so definition attributes should not repeat on every member row.
    6. Only when the user commits a completely valid staged preview.
    7. Yes. Member canonical values are unique within a dimension, and the dimension supplies their context.
    8. Sort order controls sibling presentation and may change; IDX is a stable identity segment assigned when the member is first saved.
    9. They are promoted to the removed member's parent; children of a removed root become roots.
    10. No. Current DIM09 is create-only; use DIM02 to maintain an existing dimension.
    11. Within the dimension named by `dimension_canonical_name` on that MDMEM row. A parent must be another uploaded member of that same dimension.

## Related material

- [Core business objects](../concepts/core-business-objects.md)
- [Dimension drilldowns](../analysis/dimension-drilldowns.md)
- [Transaction-code reference](../reference/transaction-codes.md)
- [File and field reference](../reference/file-and-field-reference.md)

## Product applicability

This Draft applies to the CtrlVector Dimension Master Workbench and DIM bulk-task behavior reviewed on 30 July 2026. Revalidate it when DIM interaction, hierarchy deletion, active-member filtering, IDX construction, or the `MDDIM`/`MDMEM` contract changes.
