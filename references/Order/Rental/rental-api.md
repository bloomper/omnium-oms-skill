# Rental API

Source: https://docs.omnium.no/docs/Order/Rental/rental-api

> API reference for the Omnium Rental API. Create and manage rental product instances, check availability, and add rental items to carts.

The Rental API manages rental product instances and integrates them into the cart and order flow.

## Authentication

All rental endpoints require an API key with **product-level access**.

```
Authorization: Bearer {your-api-key}
```

## Base URL

```
https://api.omnium.no/api/rentals
```

* * *

## Create Rental Products

Creates a specified number of rental product instances from a source product. Each instance is a distinct physical unit that can be individually booked.

```
POST /api/rentals/CreateRentalProducts
```

### Request body

```json
{
  "sourceProductId": "prod-bicycle-touring-28",
  "storeId": "store-oslo-west",
  "count": 5,
  "warehouseCode": "WH-OSLO",
  "prices": [
    {
      "amount": 250.00,
      "currency": "NOK"
    },
    {
      "amount": 1500.00,
      "currency": "NOK",
      "priceType": "Deposit"
    }
  ]
}
```

### Request properties

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| `sourceProductId` | string | Yes | ID of the source product to create rental instances from |
| `storeId` | string | Yes | Store to associate the rental instances with |
| `count` | integer | Yes | Number of rental instances to create |
| `warehouseCode` | string | No | Warehouse to reduce stock from when creating the instances |
| `prices` | array | No | Prices to apply to the created instances. Use `priceType: "Deposit"` to mark a deposit price. |

### Response

```json
{
  "isSuccessful": true,
  "errorMessage": null,
  "createdProducts": [...],
  "inventoryReduced": 5
}
```

### Response properties

| Property | Type | Description |
| --- | --- | --- |
| `isSuccessful` | bool | Whether the operation succeeded |
| `errorMessage` | string | Error details if `isSuccessful` is `false` |
| `createdProducts` | array | List of product objects created |
| `inventoryReduced` | integer | Quantity deducted from the source product's inventory |

* * *

## Get Rental Products

Returns all rental product instances in a store.

```
GET /api/rentals/GetRentalProducts?storeId={storeId}
```

### Query parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `storeId` | string | Yes | Store ID to retrieve rental products for |

### Response

Returns a list of products where `isRental` is `true`. For the full product model, see the [swagger documentation](https://api.omnium.no/documentation/index.html).

* * *

## Get Rental Source Products

Returns all source products that have rental instances in a store.

```
GET /api/rentals/GetRentalSourceProducts?storeId={storeId}
```

### Query parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `storeId` | string | Yes | Store ID to filter by |

### Response

Returns a list of source products. For the full product model, see the [swagger documentation](https://api.omnium.no/documentation/index.html).

* * *

## Get Rental Products by Source

Returns rental instances associated with one or more source product SKUs.

```
POST /api/rentals/GetRentalProductsBySource
```

### Request body

```json
{
  "sourceProductSkuIds": ["bicycle-touring-28-black", "bicycle-touring-28-silver"],
  "storeId": "store-oslo-west"
}
```

### Request properties

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| `sourceProductSkuIds` | array | Yes | Source product SKU IDs to look up |
| `storeId` | string | No | Filter results to a specific store |

* * *

## Get Availability

Returns availability periods and active bookings for rental products derived from a source product SKU.

```
POST /api/rentals/GetAvailability
```

### Request body

```json
{
  "sourceProductSkuId": "bicycle-touring-28-black",
  "rentalProductIds": ["rental-001", "rental-002"],
  "from": "2026-06-01T00:00:00Z",
  "to": "2026-08-31T00:00:00Z",
  "storeId": "store-oslo-west"
}
```

### Request properties

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| `sourceProductSkuId` | string | Yes | Source product SKU to check availability for |
| `rentalProductIds` | array | No | Limit results to specific rental instance IDs. Omit to include all instances. |
| `from` | datetime | No | Start of the availability window |
| `to` | datetime | No | End of the availability window |
| `storeId` | string | No | Filter to a specific store |

### Response

```json
{
  "sourceProductSkuId": "bicycle-touring-28-black",
  "rentalProducts": ["rental-001", "rental-002", "rental-003"],
  "periods": [
    {
      "from": "2026-06-01T00:00:00Z",
      "to": "2026-06-14T00:00:00Z",
      "products": ["rental-001", "rental-002", "rental-003"]
    },
    {
      "from": "2026-06-15T00:00:00Z",
      "to": "2026-06-21T00:00:00Z",
      "products": ["rental-002", "rental-003"]
    }
  ],
  "bookings": [
    {
      "rentalProductId": "rental-001",
      "projectId": "proj-8821",
      "projectName": "Rental — John Smith",
      "customerName": "John Smith",
      "rentalFrom": "2026-06-15T00:00:00Z",
      "rentalTo": "2026-06-21T00:00:00Z"
    }
  ]
}
```

### Response properties

| Property | Type | Description |
| --- | --- | --- |
| `sourceProductSkuId` | string | Source product SKU the results relate to |
| `rentalProducts` | array | All rental instance IDs considered |
| `periods` | array | Contiguous time periods and which instances are available during each |
| `periods[].from` | datetime | Period start |
| `periods[].to` | datetime | null | Period end. `null` indicates an open-ended period. |
| `periods[].products` | array | Rental instance IDs available during this period |
| `bookings` | array | Active bookings within the queried window |
| `bookings[].rentalProductId` | string | Rented instance ID |
| `bookings[].projectId` | string | ID of the rental tracking project |
| `bookings[].projectName` | string | Project name |
| `bookings[].customerName` | string | Customer name |
| `bookings[].rentalFrom` | datetime | Booking start |
| `bookings[].rentalTo` | datetime | Booking end |

* * *

## Add Rental Product to Cart

Adds a rental line item to a cart. Optionally adds a deposit line item in the same call.

```
POST /api/rentals/AddRentalProductToCart
```

### Request body

```json
{
  "cartId": "cart-a1b2c3d4",
  "rentalProductSkuId": "rental-001",
  "rentalFrom": "2026-06-15T00:00:00Z",
  "rentalTo": "2026-06-21T00:00:00Z",
  "addDeposit": true
}
```

### Request properties

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| `cartId` | string | Yes | Cart to add the rental line to |
| `rentalProductSkuId` | string | Yes | SKU of the rental instance to add |
| `rentalFrom` | datetime | Yes | Rental period start |
| `rentalTo` | datetime | Yes | Rental period end |
| `addDeposit` | bool | No | Whether to add a deposit line item. Defaults to `true`. |

The rental line item quantity is calculated as `(rentalTo.Date − rentalFrom.Date) + 1` days. The line price is the rental product's daily rate.

If you set `addDeposit: false` and need to add the deposit later, use the [Add Rental Deposit to Cart](rental-api.md#add-rental-deposit-to-cart) endpoint.

* * *

## Add Rental Deposit to Cart

Adds a deposit line item to a cart for an existing rental line. Use this when you did not include the deposit in the initial `AddRentalProductToCart` call.

```
POST /api/rentals/AddRentalDepositToCart?cartId={cartId}&rentalProductSkuId={rentalProductSkuId}
```

### Query parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `cartId` | string | Yes | Cart to add the deposit to |
| `rentalProductSkuId` | string | Yes | SKU of the rental instance the deposit is for |

The deposit line item will have `DepositFor` set to `rentalProductSkuId` and a tax rate of `0`.

* * *

## Delete Rental Products

Deletes one or more rental product instances. Optionally reclaims their inventory back to a warehouse.

```
DELETE /api/rentals/DeleteRentalProducts
```

### Request body

```json
{
  "productIds": ["rental-001", "rental-005"],
  "reclaimInventory": true,
  "reclaimToWarehouse": "WH-OSLO"
}
```

### Request properties

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| `productIds` | array | Yes | IDs of rental instances to delete |
| `reclaimInventory` | bool | No | If `true`, returns inventory to the warehouse. Defaults to `false`. |
| `reclaimToWarehouse` | string | No | Warehouse code to receive the reclaimed inventory. Required when `reclaimInventory` is `true`. |

Deleting a rental product does not cancel existing bookings or projects linked to that instance. Verify there are no active rentals before deleting.

* * *

For the full interactive API reference, see the [swagger documentation](https://api.omnium.no/documentation/index.html).
