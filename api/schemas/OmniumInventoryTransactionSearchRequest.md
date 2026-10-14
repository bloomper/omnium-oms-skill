# OmniumInventoryTransactionSearchRequest

Contains all parameters for doing an inventory transaction search

**Fields:**

- `take`: integer (int32) — Number of items to take (page size)
- `page`: integer (int32) — Page number (starts from 1)
- `productId`: string, nullable — Filter by product ID
- `skuId`: string, nullable — Filter by SKU ID
- `purchaseOrderId`: string, nullable — Filter by purchase order ID
- `userId`: string, nullable — Filter by user ID
- `orderId`: string, nullable — Filter by order ID
- `warehouseCodes`: List<string>, nullable, max length 250 — Filter by warehouse codes
- `searchText`: string, nullable — Search text for general search
- `from`: string (date-time), nullable — Start date for transaction date range
- `to`: string (date-time), nullable — End date for transaction date range

**Used directly by endpoints in:**

- [Products | Inventory](../endpoints/products/inventory.md)
