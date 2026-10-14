# Suppliers

Source: https://api.omnium.no/documentation

> Suppliers

Generated from the Omnium OpenAPI spec by `scripts/sync-api.ts` — do not edit by hand.
Common conventions and standard error responses (429, 500) are in `api/_INDEX.md`.

---

## `GET /api/Suppliers/{supplierId}`

**Get supplier**

Operation ID: `SuppliersGetSupplier`

| Param | In | Type | Required | Description |
|-------|----|------|----------|-------------|
| `supplierId` | path | string | Yes |  |

**Responses:**

- 200: [`OmniumSupplier`](../schemas/OmniumSupplier.md) — Success
- 400: string — Bad Request
- 404: string — Not Found

---

## `DELETE /api/Suppliers/{supplierId}`

**Delete supplier**

Operation ID: `SuppliersDelete`

| Param | In | Type | Required | Description |
|-------|----|------|----------|-------------|
| `supplierId` | path | string | Yes |  |

**Responses:**

- 200: Supplier deleted
- 400: Invalid request
- 404: Supplier not found

---

## `GET /api/Suppliers/{supplierId}/{versionId}`

**Get supplier version**

Operation ID: `SuppliersGetSupplierVersion`

| Param | In | Type | Required | Description |
|-------|----|------|----------|-------------|
| `supplierId` | path | string | Yes |  |
| `versionId` | path | string | Yes |  |

**Responses:**

- 200: [`OmniumSupplier`](../schemas/OmniumSupplier.md) — Success
- 400: string — Bad Request
- 404: string — Not Found

---

## `POST /api/Suppliers/Search`

**Search suppliers**

Operation ID: `SuppliersSearch`

Search suppliers

**Request body:** [`OmniumSupplierSearchRequest`](../schemas/OmniumSupplierSearchRequest.md) — Search request object for suppliers

**Responses:**

- 200: [`OmniumSupplierOmniumSearchResult`](../schemas/OmniumSupplierOmniumSearchResult.md) — Success
- 400: string — Bad Request

---

## `PUT /api/Suppliers`

**Update supplier**

Operation ID: `SuppliersUpdate`

Update supplier

**Request body:** [`OmniumSupplier`](../schemas/OmniumSupplier.md) — Supplier

**Responses:**

- 200: [`OmniumSupplier`](../schemas/OmniumSupplier.md) — Success
- 400: string — Bad Request

---

## `PUT /api/Suppliers/UpdateMany`

**Update many suppliers**

Operation ID: `SuppliersUpdateMany`

Update many suppliers

**Request body:** List<[`OmniumSupplier`](../schemas/OmniumSupplier.md)> — Suppliers

**Responses:**

- 200: Success
- 400: string — Bad Request
