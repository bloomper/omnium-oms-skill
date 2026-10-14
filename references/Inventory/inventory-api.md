# API Reference

Source: https://docs.omnium.no/docs/Inventory/inventory-api

## About Inventory

Products in Omnium are enriched with inventory items. The inventory item carries information about:

*   Physical inventory
*   Reserved inventory
*   Available inventory
*   Inventory location

An inventory item identifier/key is created from the variant and warehouse code. The warehouse code is the ID of the store/warehouse where the inventory is located.

**Note**: Adding an inventory item with a warehouseCode and a variant will override any inventory item with the same warehouseCode and inventory.

### Creating Inventory Items

*   Add inventory in the GUI
*   Import/export inventory items in the GUI
*   Import through API

* * *

### Inventory Item Model

For details, refer to the [Omnium Inventory Model](https://api.omnium.no/documentation/index.html#/model-OmniumWarehouseInventory).

* * *

### Inventory Sample

Inventory items for the store **OmniumBikeShop** could look like this:

```json
{
  "sku": "Topeak-43543512",
  "warehouseCode": "WebShop",
  "inventory": 19,
  "reservedInventory": 3,
  "location": null
}
```

In the Omnium GUI, the inventory item can be displayed as shown below:

![Store in the GUI](https://docs.omnium.no/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fomnium_inventory.4daa10de.jpg&w=3840&q=75)

* * *

### Create Inventory Items

Add inventory items to Omnium by posting a list of `OmniumInventoryItem` to the [add many endpoint](https://api.omnium.no/documentation/index.html#/Inventory/post_api_Inventory_AddMany). If the item exists, it will be overridden by the object posted to the endpoint.

For more details, visit the [Create inventory items documentation](https://api.omnium.no/documentation/index.html#/Inventory/post_api_Inventory_AddMany).

* * *

### Get Inventory Item

#### Get Single Inventory Item

Inventory items are fetched via a GET request to the [inventory endpoint](https://api.omnium.no/documentation/index.html#/Inventory/get_api_Inventory__id_).

An optional parameter for warehouse IDs is available. If `warehouseIds` is empty, all inventory items for the SKU will be returned.

For more details, visit the [Get inventory item documentation](https://api.omnium.no/documentation/index.html#/Inventory/get_api_Inventory__id_).

#### Search Inventory Items

Sample request for a delta query:

```
POST /api/inventory/search
{
  "LastModified": "2019-11-21T17:32:28Z"
}
```

Sample request for inventory for a specific market:

```
POST /api/inventory/search
{
  "MarketId": "NOR"
}
```

Sample response if the number of inventory items exceeds 2000 (totalHits):

```json
{
  "totalHits": 41244,
  "result": [
    {
      "sku": "102103",
      "warehouseCode": "40",
      "inventory": 33,
      "reservedInventory": 7,
      "location": "32D-A2"
    },
    {
      "sku": "102104",
      ...
    }
  ],
  "scrollId": "2f842421e06e4c60bb2c4f0d8b3ef60b"
}
```

**Note**: If the total number of items exceeds 2000, the response will contain a **Scroll ID**, which can be used with the Scroll endpoint to fetch all remaining items.

For more details, visit the [Search inventory items documentation](https://api.omnium.no/documentation/index.html#/Inventory/post_api_Inventory_Search).

* * *

### Update Inventory Item

To update an inventory item, send a PUT request with the inventory object to the inventory update endpoint.

For more details, visit the [Update inventory item documentation](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Inventory/InventoryUpdate).

#### Update Multiple Inventory Items

To update multiple inventory items, send a PUT request to the [inventory update many endpoint](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Inventory/InventoryUpdateMany).

* * *

### Delete Inventory Item

To delete an inventory item, send a DELETE request to the inventory endpoint:

For more details, visit the [Delete inventory item documentation](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Inventory/InventoryDelete).

**Note**:The inventory item will be permanently deleted and cannot be recovered.

* * *

### More Inventory Information

#### ATP Values

**Available to Promise** (ATP) refers to the available quantities of the requested product and its expected delivery due dates.

For more details, visit the [ATP model documentation](https://api.omnium.no/documentation/index.html#/model-OmniumInventoryAtp).

* * *

## API Integrations – Best Practices

### Updating Inventory in Omnium

*   Use the [`InventoryUpdateMany`](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Inventory/InventoryUpdateMany) endpoint.

    *   Automatically creates inventory items if they don't already exist.
    *   Skips updates for unchanged items to maintain high performance, even with a large amount of inventory items.
    *   Will only create inventory transactions for actual changes, so the transaction list stays clean and relevant.
*   Use batch sizes of 1000 for stability and performance.

*   If reservations are **managed externally** (i.e., outside Omnium):

    *   Set `isReservedInventoryOverwritten = true` in your `UpdateMany` request.
    *   This overwrites reservation values on the items.
    *   If left `null` or `false`, existing reservations remain unchanged.
*   **Avoid creating zero-value inventory items**:

    *   This reduces the number of inventory items and improves performance.
    *   Treat missing inventory items as having a value of zero.

* * *

### Fetching Inventory from Omnium

*   **Full Sync** - Use the [`InventoryScroll`](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Inventory/InventoryScroll) endpoint:

    *   Intended only for **occasional** full synchronizations.
    *   Note: It is a resource-heavy operation.
*   **Delta Updates** - Use the [`InventorySearch`](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Inventory/InventorySearch) endpoint:

    *   Set the `LastModified` date parameter to fetch only changed data.
    *   Ideal for frequent, lightweight sync operations.
    *   Set `IsBatchesExcluded = true` when batch data is not required.
        *   This significantly reduces the payload size and improves performance. ([Learn more about FIFO](../Inventory.md#fifo))

* * *

## Inventory handling in product search API

### Overview

The [Product Search API](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Search/ProductsSearchProducts) returns product data, including an `inventory` field. This field is not queried from live inventory data—it is a snapshot stored on the product itself.

### Architecture

Omnium stores inventory and products separately:

*   **Inventory data** — The source of truth, updated in real-time via the Inventory API
*   **Products** — Each product has an `inventory` field that holds a copy of relevant inventory data

The Product Search API returns product data, including the `inventory` field. To keep this field in sync with the actual inventory data, a scheduled task periodically copies changes.

### Synchronization

The [`ProductInventoryStatusScheduledTask`](../configuration/scheduled-tasks/products/product-inventory-status.md) synchronizes inventory data to products. This is a **delta task** that processes changes since its last run.

How it works:

1.  On each run, the task queries for inventory changes since the previous execution
2.  It updates the `inventory` field on affected products
3.  It refreshes the product search to make changes visible

Data freshness is determined by the task schedule. For example:

*   Schedule `*/5 * * * *` (every 5 minutes) → inventory data on products can be up to 5 minutes stale
*   Schedule `*/1 * * * *` (every minute) → inventory data on products can be up to 1 minute stale

The task processes only changes since the last run, so execution time depends on the volume of inventory changes. Initial runs or periods with high inventory activity will take longer. See the [task configuration documentation](../configuration/scheduled-tasks/products/product-inventory-status.md) for setup details.

Additionally, the [`CalculateAndUpdateProductInventory`](../Order/workflow-steps/inventory/calculate-and-update-product-inventory.md) workflow step can be used in order workflows to immediately update product inventory for all SKUs in an order.

### Behavior in Search Results

*   If a product has inventory in the queried market, the `inventory` field will be included in the response.
*   After an inventory change, the updated data will not appear in Product Search results until the synchronization runs (scheduled task or workflow step).
*   New products are automatically initialized with inventory status when the task runs.
*   New warehouses, or changes to existing warehouses are also handled automatically in the scheduled task

### Example request

#### Request

```json
{
  "page": 1,
  "take": 200,
  "storeId": "bike-shop-web-no",
  "productLanguage": "no",
  "sortOrder": "CreatedAscending",
  "isFacetsDisabled": true,
  "isActive": true,
  "excludedFields": {
    "isEverythingExcluded": true
  },
  "includedFields": {
    "specificFields": ["inventory"]
  },
  "productIds": ["V-553456", "DMR Vault Pedal"]
}
```

### Response examples

#### Case 1: Inventory Exists

```json
{
  "productId": "V-553456",
  "inventory": [{
    "storeId": "bike-shop-web-no",
    "quantity": 10
  }]
}
```

#### Case 2: No inventory (Field absent)

```json
{
  "productId": "V-553457"
}
```

### Important notes

*   The `inventory` field will not be present if the product has no inventory.
*   If inventory was recently updated, the scheduled task may not have processed the changes yet.
