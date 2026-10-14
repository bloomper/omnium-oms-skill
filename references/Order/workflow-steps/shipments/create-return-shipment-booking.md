# Create Return Shipment Booking

Source: https://docs.omnium.no/docs/Order/workflow-steps/shipments/create-return-shipment-booking

> Create a booking for return shipment with the shipping provider.

## Overview

The **Create Return Shipment Booking** step creates a return shipping booking with the carrier. This generates a return label that can be included with the shipment or sent to the customer.

## Identifier

| Property | Value |
| --- | --- |
| **Key** | `CreateReturnShipmentBooking` |
| **Group** | Shipments |
| **Applicable Statuses** | New |

## Configuration Properties

This step has no configurable properties. Return product is configured on the shipping option.

## Behavior

### What It Does

1.  Identifies the shipment to create return booking for:
    *   Uses current shipment if at shipment level
    *   Uses last shipped shipment if at order level
2.  Looks up shipping option for the shipment's method
3.  Validates `ShipmentReturnProduct` is configured on the option
4.  Creates a return shipment booking with the shipping provider
5.  Returns return label link in result

### Prerequisites

*   Shipment must exist (shipped for order level)
*   Shipping option must have `ShipmentReturnProduct` configured
*   Shipping provider must support return bookings

### Side Effects

*   Return booking created with carrier
*   Return label generated
*   Return tracking number assigned

## Business Cases

### When to Use

*   **Proactive returns**: Include return label in package
*   **Easy returns**: Generate return label when order ships
*   **Return policy**: Automatically provide return shipping

### Example Scenarios

**Scenario 1: Standard Return Label** Order ships. Step creates return booking, returns PDF label link.

**Scenario 2: No Return Product** Shipping option doesn't have return product configured. Step returns warning.

**Scenario 3: No Shipped Shipments** No shipments in shipped status at order level. Step returns warning.

## Error Handling

| Condition | Result | Continues Workflow? |
| --- | --- | --- |
| No shipments | Warning | Yes |
| No shipping option | Warning | Yes |
| No return product configured | Warning | Yes |
| Booking successful | Success (with label link) | Yes |
| Booking failed | Error | No (cancels workflow) |

## Related Steps

*   [Complete Shipment](complete-shipment.md) - Ships the order first
*   [Print Shipping Label](print-shipping-label.md) - Print the return label
