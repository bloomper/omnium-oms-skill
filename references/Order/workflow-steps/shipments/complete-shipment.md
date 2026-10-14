# Complete Shipment

Source: https://docs.omnium.no/docs/Order/workflow-steps/shipments/complete-shipment

> Finalize shipments and notify shipping providers about completed shipments.

## Overview

The **Complete Shipment** step finalizes released shipments by notifying the shipping provider and updating the shipment status to shipped. It returns shipping label links when available.

This step sets shipments to `Shipped`. Inventory reduction steps such as [Reduce Inventory and Reserved Inventory](../inventory/reduce-inventory-and-reserved-inventory.md) only process shipments in `Released` status, so they must run **before** this step. Placed after it, they find no `Released` shipments and report success without reducing any inventory.

## Identifier

| Property | Value |
| --- | --- |
| **Key** | `CompleteShipment` |
| **Group** | Shipments |
| **Applicable Statuses** | Ship, Completed, ReadyForPickup |

## Configuration Properties

This step has no configurable properties.

## Behavior

### What It Does

1.  Identifies shipments with `Released` or `SplittedShippedShipment` status
2.  If running at shipment level, only processes the current shipment
3.  For each eligible shipment:
    *   Notifies the shipping provider that the shipment is complete
    *   Retrieves shipping label links
    *   Updates shipment status to shipped
4.  Returns label links in the result for UI display

### Prerequisites

*   Shipments must be in `Released` status
*   Shipping provider must be configured

### Side Effects

*   Shipment status updated to Shipped
*   Shipping provider notified (booking created)
*   Tracking number assigned
*   Label links generated

## Business Cases

### When to Use

*   **Order fulfillment**: Final step when shipping orders
*   **Label generation**: Trigger shipping label creation
*   **Carrier notification**: Inform carrier about shipment

### Example Scenarios

**Scenario 1: Standard Ship** Order picked and ready. Step completes shipment, notifies carrier, returns label PDF link.

**Scenario 2: Multi-Shipment** Order has multiple shipments. Running at order level completes all released shipments.

**Scenario 3: Click-and-Collect** Pickup order ready. Step completes shipment for record-keeping (may not generate carrier label).

**Scenario 4: Already Shipped** Shipment already in Shipped status. Step returns warning.

## Error Handling

| Condition | Result | Continues Workflow? |
| --- | --- | --- |
| No released shipments | Warning | Yes |
| Completion successful | Success (with label links) | Yes |
| Provider error | Error | No (cancels workflow) |

## Related Steps

*   [Prepare Shipment Delivery](prepare-shipment-delivery.md) - Pre-notify shipping provider
*   [Print Shipping Label](print-shipping-label.md) - Print the generated label
*   [Update Shipment Status](update-shipment-status.md) - Update status manually
