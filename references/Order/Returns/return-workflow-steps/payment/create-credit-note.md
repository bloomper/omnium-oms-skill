# Create Credit Note

Source: https://docs.omnium.no/docs/Order/Returns/return-workflow-steps/payment/create-credit-note

> Generate a credit note document for the return transaction.

## Overview

The **Create Credit Note** step generates a credit note document for the return. Credit notes serve as official documentation of the refund and can be used for accounting, customer records, and regulatory compliance.

## Identifier

| Property | Value |
| --- | --- |
| **Key** | `CreateCreditNote` |
| **Group** | Payments |

## Configuration Properties

This step has no configurable properties. Credit note format and numbering follow the tenant's invoice/credit note settings.

## Behavior

### What It Does

1.  Generates a unique credit note number
2.  Creates credit note document with return details
3.  Stores credit note reference on the return order
4.  May trigger document generation (PDF) depending on configuration

### Prerequisites

*   Return order must have calculated totals
*   Credit note numbering sequence must be configured

### Side Effects

*   Creates credit note record
*   Assigns credit note number to return
*   May generate PDF document

## Business Cases

### When to Use

*   When official credit documentation is required
*   For accounting and audit purposes
*   When customers need refund documentation
*   For regulatory compliance (VAT/tax reporting)

### Example Scenarios

**Standard Credit Note** Return processed for $150. Credit note generated with unique number (e.g., CN-2024-00123) documenting the refund amount, items returned, and original order reference.

**Partial Return Credit Note** Customer returns 2 of 5 items. Credit note lists only the returned items and their refund values.

## Error Handling

| Condition | Result | Continues Workflow? |
| --- | --- | --- |
| Credit note created | Success | Yes |
| Number sequence unavailable | Error | Depends on StopOnError |
| Document generation failed | Warning | Yes |

## Related Steps

*   [Credit Return](credit-return.md) - Process the actual refund
*   [Export Payments](export-payments.md) - Sync payment data externally
