# Build the foundation

!!! warning "Page status: Draft"
    The sequence has been exercised in a development environment. Production environment and identity setup remain outside this Draft.

## Entry criteria

- The discovery questionnaire is complete.
- The application opens without a red application error.
- The implementation role can access the required TCodes.
- A clean tenant context has been confirmed through the approved environment process.

## 1. Create dimensions

Use DIM01 to create each Dimension header, then DIM02 to add or maintain members. Verify the saved definition and hierarchy in DIM03.

For the workshop pattern:

| Dimension | Leaf members |
| --- | --- |
| Mode of Transport | Air, Ocean, Surface |
| Country | Canada, United States |
| Product | Laptops, Desktops |

Record the business display name, canonical identity, member order, hierarchy, and status. Use display transactions after each object family rather than assuming that navigation alone proves persistence.

See [Dimensions](../configuration/dimensions.md) for detailed field behavior and import guidance.

## 2. Create the Calendar and Periods

Create the Calendar before its Periods.

The verified example uses a fiscal year beginning 1 November and ending 31 October:

| Period | Start | End | Sequence | Year | Within year |
| --- | --- | --- | ---: | ---: | ---: |
| FY26-Q1 | 2025-11-01 | 2026-01-31 | 1 | 2026 | 1 |
| FY26-Q2 | 2026-02-01 | 2026-04-30 | 2 | 2026 | 2 |
| FY26-Q3 | 2026-05-01 | 2026-07-31 | 3 | 2026 | 3 |
| FY26-Q4 | 2026-08-01 | 2026-10-31 | 4 | 2026 | 4 |

Configure the useful implementation horizon, not only the two periods in the first Bridge. See [Versions, calendars, and periods](../configuration/time-and-versions.md) for the distinction between Sequence and Within year.

## 3. Create the Version

Create and verify the business Version used by the workspaces. The example uses **Actuals** with the meaning “Observed results.”

## 4. Record access and production gaps

In a development-only installation, user login, tenant selection, and server-based multi-user persistence may not yet be part of the product flow. Record these as deployment gaps. Do not invent a production procedure around a local development database.

## Exit evidence

- [ ] Every Dimension appears in DIM03 with the approved active leaves
- [ ] The Calendar appears in display mode with the approved fiscal definition
- [ ] All required Periods appear in chronological Sequence
- [ ] The Version appears with the correct business meaning and status
- [ ] Any access, tenant, or production-architecture gap is recorded

Next: [Configure the semantic model](semantic-model.md).
