# Configure versions, calendars, and periods

!!! info "Page status: Planned"
    Time-axis and version-maintenance procedures still require validation.

## Chapter purpose

Explain how CtrlVector gives business meaning and governed time context to submitted values and comparisons.

## Planned coverage

- Actual, Budget, Forecast, and other version meanings;
- calendars, period grains, parent periods, and sequence;
- compatibility of baseline and current observations;
- create, change, display, and bulk-maintenance transactions;
- worked examples, verification, and common date-model errors.

## PER02 period-position fields

PER02 uses three related fields to place a period on a calendar. They answer different questions:

| Field | Meaning | Example for Q1 FY26 |
| --- | --- | ---: |
| **Year** | The calendar or fiscal year to which the period belongs. | `2026` |
| **Within year** | The period's position inside that year. It resets when the next year begins. | `1` |
| **Sequence** | The period's chronological position across the full calendar and grain. It does not reset at a year boundary. | `9` |

For a quarterly calendar, the values might look like this:

| Period | Year | Within year | Sequence |
| --- | ---: | ---: | ---: |
| Q4 FY25 | 2025 | 4 | 8 |
| Q1 FY26 | 2026 | 1 | 9 |
| Q2 FY26 | 2026 | 2 | 10 |

These fields support different relative-period operations:

- **Previous period** uses Sequence within the same calendar and period grain. From Q1 FY26, the preceding sequence identifies Q4 FY25.
- **Same period in the prior year** uses Year and Within year within the same calendar and period grain. From Q1 FY26, CtrlVector looks for year `2025` and within-year position `1`, identifying Q1 FY25.

!!! tip "How to number Sequence"
    The starting number is not important. Sequence values must be unique and chronologically ordered within the same calendar and period grain. Consecutive values are recommended because they are easiest to maintain and review, although CtrlVector's prior-period lookup selects the nearest lower sequence and does not require every integer to be present.

For other grains, **Within year** follows the same principle: March is normally `3`, week 15 is `15`, and the second fiscal period is `2`.
