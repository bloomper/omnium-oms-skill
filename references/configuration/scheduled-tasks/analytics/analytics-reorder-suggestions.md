# Analytics Reorder Suggestions

Source: https://docs.omnium.no/docs/configuration/scheduled-tasks/analytics/analytics-reorder-suggestions

> Generate product reorder suggestions based on sales patterns

## Overview

This task generates product reorder suggestions by analyzing historical sales data and current inventory levels. It performs a full reindex of all reorder suggestions, making it suitable for periodic complete recalculations.

## Identifier

| Property | Value |
| --- | --- |
| **Implementation Type** | `AnalyticsReorderSuggestionsScheduledTask` |
| **Group** | Analytics |
| **Type** | Full |

* * *

## When to Use

Enable this task when you need:

*   Automatic inventory replenishment recommendations
*   Purchase order planning assistance
*   Stock level optimization
*   Demand forecasting support

* * *

## Configuration Properties

This task has no configurable properties.

* * *

## Behavior

### What It Does

1.  Analyzes historical sales data across all products
2.  Calculates reorder points based on sales velocity
3.  Compares current inventory levels against calculated minimums
4.  Generates reorder suggestions for products below threshold
5.  Indexes all suggestions for access via API and UI

### Prerequisites

*   Historical order data must exist for accurate suggestions
*   Inventory levels must be tracked in the system
*   Products must have sales history for meaningful recommendations

### Side Effects

*   Rebuilds the entire reorder suggestions index
*   Previous suggestions are replaced with newly calculated ones
*   Resource-intensive operation that may impact system performance

* * *

## Example Configuration

```json
{
    "ImplementationType": "AnalyticsReorderSuggestionsScheduledTask",
    "Schedule": "0 4 * * *",
    "IsDisabled": false
}
```

* * *

## Recommended Schedule

Run daily during off-peak hours (`0 4 * * *` - 4:00 AM) as this is a resource-intensive full reindex operation. For more frequent updates, use the delta version instead.

* * *

## Related Tasks

*   [Analytics Reorder Suggestions Delta](analytics-reorder-suggestions-delta.md) - Incremental updates
*   [Supplier Reorder Suggestions](../inventory/supplier-reorder-suggestions.md) - Supplier-specific suggestions
