# Update Inventory

Source: https://docs.omnium.no/docs/Order/Returns/return-workflow-steps/inventory/update-inventory

> Increase inventory levels for returned items.

## Overview

The **Update Inventory** step adds returned items back to available inventory. It respects the return type configuration and individual line item settings to determine whether each item should be restocked.

## Identifier

| Property | Value |
| --- | --- |
| **Key** | `UpdateInventory` |
| **Group** | Inventory |

## Configuration Properties

This step has no configurable properties. Inventory updates are controlled by:

*   Return type's `UpdateInventory` setting
*   Individual return line item settings
*   The "Update Stock" selection made when creating the return

## Behavior

### What It Does

1.  Iterates through all return line items
2.  Checks if each item should update inventory based on:
    *   Return type configuration (`UpdateInventory` property)
    *   Line-level override settings
3.  Increases warehouse inventory for applicable items
4.  Records inventory adjustment transactions

### Prerequisites

*   Return must have line items with valid SKUs
*   Products must exist in the inventory system
*   Warehouse must be identified on the return

### Side Effects

*   Increases available inventory quantities
*   Creates inventory adjustment records
*   May trigger inventory sync to external systems
*   Updates product availability status

## Business Cases

### When to Use

*   Restocking returned items that can be resold
*   Adjusting inventory after receiving returns
*   Maintaining accurate stock levels

### Example Scenarios

**Standard Return** Customer returns 3 units of SKU-123. Return type has `UpdateInventory: true`. Inventory increases by 3 units.

**Defective Item** Customer returns defective item. Return type "Defective" has `UpdateInventory: false`. Inventory remains unchanged (item may be sent to supplier or disposed).

**Mixed Return** Customer returns 2 resellable items and 1 defective. Only the 2 resellable items update inventory.

**Wrong Item Sent** Item is returned because warehouse shipped wrong product. Return type "WrongItem" has `UpdateInventory: true` since item is still sellable.

## Warehouse Selection

Inventory is updated in the warehouse associated with the return. For split orders with `IsReturnSplitOrdersToMainWarehouse` enabled, returns go to the main warehouse.

## Error Handling

| Condition | Result | Continues Workflow? |
| --- | --- | --- |
| Inventory updated | Success | Yes |
| No items to update (none restockable) | Success | Yes |
| Line missing store/stock flag or non-restockable return type | Skipped silently (line is excluded from the updated count) | Yes |
| Order or return not found | Error | Depends on StopOnError |
| Unexpected error during inventory update | Error | No |

## Related Steps

*   [Credit Return](../payment/credit-return.md) - Often runs together to complete return
*   [Update Order Status](../status/update-order-status.md) - Update original order after inventory adjustment
