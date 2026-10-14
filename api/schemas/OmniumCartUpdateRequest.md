# OmniumCartUpdateRequest

**Fields:**

- `cartId`: string, nullable — ID of cart to edit
- `marketId`: string, nullable — Market ID for new cart - default market if null
- `storeId`: string, nullable — Store ID for new cart - defaults to null
- `customerId`: string, nullable — CustomerId for the new cart - defaults to null
- `lineItemRequests`: List<[`OmniumLineItemUpdateRequest`](OmniumLineItemUpdateRequest.md)>, nullable — List of Product SKU ids and quantity (defaults to 1 if not provided). (Required)
- `priceStoreId`: string, nullable — If set here, it applies to all items in LineItemRequests unless a specific {PriceStoreId} is provided at the line item level.

**Used directly by endpoints in:**

- [Carts | Add](../endpoints/carts/add.md)
