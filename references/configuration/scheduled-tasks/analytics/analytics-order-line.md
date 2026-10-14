# Analytics Order Line

Source: https://docs.omnium.no/docs/configuration/scheduled-tasks/analytics/analytics-order-line

> Index order line analytics for sales reporting and analysis

## Overview

This task indexes analytics for order line items, providing detailed sales data for reporting and business intelligence. It processes orders modified since the last run and creates analytics records for each order line.

## Identifier

| Property | Value |
| --- | --- |
| **Implementation Type** | `AnalyticsOrderLineScheduledTask` |
| **Group** | Analytics |
| **Type** | Delta |

* * *

## When to Use

Enable this task when you need:

*   Detailed sales reporting by product
*   Order line-level analytics
*   Revenue breakdown analysis
*   Product performance tracking

* * *

## Configuration Properties

This task has no configurable properties.

* * *

## Behavior

### What It Does

1.  Checks if more than 5 days have passed since last run (skips if so)
2.  Retrieves orders modified since the last run (with 1-minute overlap for safety)
3.  Processes each order's line items
4.  Creates analytics records for reporting
5.  Supports cancellation for timeout management

### Gap Protection

If more than 5 days have passed since the last successful run, the task will skip execution and return a success status with a message indicating the skip. This prevents excessive processing after extended downtime and protects system resources.

### Prerequisites

*   Orders with line items must exist
*   Analytics indexing services must be configured

### Side Effects

*   Updates the analytics order line index
*   Gaps may occur in analytics if the task doesn't run for more than 5 days

* * *

## Example Configuration

```json
{
    "ImplementationType": "AnalyticsOrderLineScheduledTask",
    "Schedule": "*/10 * * * *",
    "IsDisabled": false
}
```

* * *

## Recommended Schedule

Run every 10-15 minutes (`*/10 * * * *`) to maintain current order line analytics. Ensure the task runs at least once within any 5-day period to avoid skipping.

* * *

## Related Tasks

*   [Add Stats Orders](add-stats-orders.md) - Order statistics queue
*   [Analytics Interaction](analytics-interaction.md) - Customer interaction analytics
