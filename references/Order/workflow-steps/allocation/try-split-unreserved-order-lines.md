# Try Split Unreserved Order Lines

Source: https://docs.omnium.no/docs/Order/workflow-steps/allocation/try-split-unreserved-order-lines

> Split order lines without inventory reservation using configurable filters and allocation schemes.

## Overview

The **Try Split Unreserved Order Lines** step is a highly configurable splitting mechanism that moves unreserved order lines to new shipments based on various criteria. It offers the most flexibility for complex distribution scenarios.

## Identifier

| Property | Value |
| --- | --- |
| **Key** | `TrySplitUnreservedOrderLines` |
| **Group** | Reallocate |
| **Applicable Statuses** | New |

## Configuration Properties

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| `WarehouseCode` | string | No | Target warehouse for split shipment |
| `StoreRoles` | string | No | Filter warehouses by role |
| `OrderLineProperties` | json | No | Filter lines by custom properties |
| `HasFutureRequestedDeliveryDate` | boolean | No | Only split lines with future delivery dates |
| `SplitToStatus` | string | No | Status to assign to new shipments |
| `ShippingMethodName` | string | No | Shipping method for new shipments |
| `AllocationScheme` | string | No | Algorithm for warehouse selection |
| `IgnoreInventory` | boolean | No | Split without checking inventory |
| `AllowLineSplitting` | boolean | No | Allow splitting quantities within a line |

## Behavior

### What It Does

1.  Identifies order lines without full inventory reservation
2.  Applies configured filters (properties, delivery date, etc.)
3.  Determines target warehouse using allocation scheme
4.  Creates new shipment(s) for matching lines
5.  Moves lines to new shipments with optional status/method changes
6.  Optionally updates order status if all shipments have the new status

### Prerequisites

*   Order must have unreserved line items (or `IgnoreInventory = true`)
*   Warehouse configuration must support the allocation scheme

### Side Effects

*   Creates new shipments
*   Moves line items between shipments
*   May update order and shipment statuses
*   May split line quantities if enabled

## Business Cases

### When to Use

*   **Complex routing rules**: When simple warehouse-based splitting isn't enough
*   **Future orders**: Separate lines with future delivery dates
*   **Multi-carrier orders**: Split to different shipping methods
*   **Property-based routing**: Route based on line item properties

### Example Scenarios

**Scenario 1: Future Delivery Separation** Configure `HasFutureRequestedDeliveryDate = true` to split pre-orders to a separate shipment.

**Scenario 2: Status-Based Workflow** Configure `SplitToStatus = "AwaitingStock"` to route unreserved items to a waiting status while in-stock items proceed.

**Scenario 3: Property Filtering** Configure `OrderLineProperties = {"isFragile": true}` to split fragile items for special handling.

**Scenario 4: Quantity Splitting** Configure `AllowLineSplitting = true` to split a line with quantity 5 into two lines (e.g., 3 available + 2 on backorder).

## Error Handling

| Condition | Result | Continues Workflow? |
| --- | --- | --- |
| Lines split successfully | Success | Yes |
| No lines to split | Success | Yes |
| Exception | Error | Depends on StopOnError |

## Related Steps

*   [Try Split Order to Warehouse](try-split-order-to-warehouse.md) - Simpler single-warehouse split
*   [Try Split Unreserved to Closest Zip](try-split-unreserved-order-lines-to-closest-zip.md) - Proximity-based splitting
*   [Try Reallocate to Other Warehouse](try-reallocate-to-other-warehouse.md) - Reallocate without creating new shipments
