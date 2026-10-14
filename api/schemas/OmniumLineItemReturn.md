# OmniumLineItemReturn

Request object for adding return line items

**Fields:**

- `lineItemId`: string, nullable — ID of line item to return
- `returnQuantity`: number (decimal) — Quantity returned
- `returnReason`: string, nullable — Return reason. Free text.
- `returnType`: string, nullable — Return type ID
- `isStockUpdated`: boolean, nullable — Whether the return order line should update stock (inventory) value. If set to 'true', the stock would increase with the same amount as the quantity on the return order line. If set to 'false', the stock would be unaffected. If set to null, return settings will decide.
- `creditAmount`: number (decimal), nullable — Amount to credit on a returnItem pr item. Total orderLine credit amount will be creditAmount * quantity.  If not specified, the item will be fully credited. Type: Decimal.
- `internalWarehouseLocation`: string, nullable — Use this to specify location within the warehouse. In case you have separate locations for returned products IE. Broken/UsedOrOpened/SellAsNew **Not required**
- `properties`: List<[`OmniumPropertyItem`](OmniumPropertyItem.md)>, nullable — Custom properties to set on the return order line
