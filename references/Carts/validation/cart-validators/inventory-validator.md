# Inventory Validator

Source: https://docs.omnium.no/docs/Carts/validation/cart-validators/inventory-validator

> Validates that all products in the cart have sufficient stock across the relevant warehouses.

## Overview

The **Inventory Validator** is the primary inventory validation mechanism. It checks that each line item in the cart has enough available stock across the relevant warehouses. It supports warehouse resolution from store configuration, shipment-level warehouse assignments, assortment filtering, and configurable order type restrictions.

## Identifier

| Property | Value |
| --- | --- |
| **Connector ID** | `inventoryValidator` |
| **Validation Type** | Inventory |
| **Validation Scope** | LineItem |

## Behavior

1.  Checks if inventory validation is enabled for the cart's order type (see Configuration below)
2.  If the cart has no line items, returns a "Cart is empty" error
3.  Resolves the relevant warehouses:
    *   Uses the cart's store and its configured available warehouses
    *   Includes warehouses referenced in shipments
    *   Falls back to all warehouses if no store is set
4.  Fetches product data and inventory data in parallel
5.  For each line item:
    *   Skips virtual products, backorder products, and products in the "service" catalog
    *   Skips products where the category has `IsInventoryValidationDisabled` set to `true`
    *   Skips non-promotion products if `IsInventoryValidationDisabledForNonPromotions` is enabled
    *   Filters inventory by assortment (product `StoreIds`) unless the order type is in `IgnoredOrderTypesForAssortmentInventoryValidator`
    *   Excludes warehouses listed in `ExcludeWarehouseIds` on the product or variant
    *   Calculates available quantity: `Quantity - ReservedQuantity + CalculatedQuantity` (negative values treated as zero)
    *   If a shipment has a specific warehouse assigned, only checks that warehouse
    *   Tracks cumulative demand per SKU across multiple line items with the same product
6.  Returns a validation error for each line item where the requested quantity exceeds available stock

## Error messages

| Condition | Translation Key | Error Code |
| --- | --- | --- |
| Cart has no line items | `CartIsEmpty` | `CartIsEmpty` |
| Insufficient stock for SKU | `InventoryValidatorErrorMessage` | `InsufficientInventory` |

## Configuration

The Inventory Validator supports optional configuration properties in the connector settings.

| Property | Type | Description |
| --- | --- | --- |
| `OrderTypes` | string | Comma-separated list of order types to validate. If empty, all order types are validated. |

### Tenant settings

The following settings from `InventoryManagement` in tenant settings also affect this validator:

| Setting | Type | Description |
| --- | --- | --- |
| `IsInventoryValidationDisabledForNonPromotions` | bool | When enabled, skips inventory validation for products that are not part of a promotion |
| `IgnoredOrderTypesForAssortmentInventoryValidator` | list | Order types that should skip assortment-based inventory filtering |

### Per-product properties

Individual products can override behavior by setting these properties:

| Property | Format | Description |
| --- | --- | --- |
| `ExcludeWarehouseIds` | Pipe-separated list (`WH1||WH2`) | Warehouses to exclude from the available stock calculation. Set on the product or a variant. |

### Example

```json
{
  "name": "inventoryValidator",
  "implementations": ["IValidator"],
  "properties": [
    { "key": "OrderTypes", "value": "Online, B2B" }
  ],
  "disableStandardErrorPolicy": false
}
```

## Exclude warehouses for a product

You can stop the validator from counting a specific warehouse's stock for a single product by adding an `ExcludeWarehouseIds` entry to the product's `Properties` list. Stock held at the listed warehouses is ignored when calculating availability for that product.

Separate multiple warehouse codes with `||`. Matching is case-insensitive.

```json
{
  "id": "12345",
  "properties": [
    { "key": "ExcludeWarehouseIds", "value": "central-warehouse-oslo-01||outlet-bergen-03" }
  ]
}
```

The property can be set on the product, on a variant, or both. When both are present, the two lists are merged.

Excluding a warehouse removes its stock from the availability total — it does not skip validation. If a line item can only be fulfilled from an excluded warehouse (for example, a shipment routed to that warehouse), the product is treated as out of stock and validation fails.
