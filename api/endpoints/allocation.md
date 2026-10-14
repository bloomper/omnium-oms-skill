# Allocation

Source: https://api.omnium.no/documentation

> Ask the allocation engine where an order or basket should ship from, without changing anything.

Generated from the Omnium OpenAPI spec by `scripts/sync-api.ts` — do not edit by hand.
Common conventions and standard error responses (429, 500) are in `api/_INDEX.md`.

---

## `POST /api/Allocation/Simulate`

**Work out where an order or basket should ship from. Nothing is written: the order, the cart and inventory are all left as they are.**

Operation ID: `AllocationSimulate`

Send exactly one subject: {orderId} or {cartId} for something already in Omnium, or
{order} or {cart} for a basket that is not. A checkout normally sends the basket.

Name several {profileKeys} to get several rankings from one run: the plans are worked out
once and each profile only contributes an order over them, so asking for three costs little more
than asking for one. The first key decides {selectedPlan}.

A checkout that already holds its own availability can pass it as {inventoryOverrides} and
skip Omnium's inventory read.

**Request body:** [`OmniumAllocationSimulationRequest`](../schemas/OmniumAllocationSimulationRequest.md)

**Responses:**

- 200: [`OmniumAllocationResult`](../schemas/OmniumAllocationResult.md) — Success
- 400: Bad Request
- 404: Not Found
