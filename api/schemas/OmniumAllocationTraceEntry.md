# OmniumAllocationTraceEntry

One step in the engine's reasoning.

**Fields:**

- `stage`: string, nullable — Line, Shipment or Plan
- `kind`: string, nullable — Info, WarehouseExcluded, WarehouseRestricted, WarehousePreferred, PlanRejected, Repaired or Scored
- `message`: string, nullable — Why, as a stable key such as InsufficientStock
- `ruleId`: string, nullable — The rule responsible, when a rule was
- `warehouseId`: string, nullable
- `lineId`: string, nullable
- `planId`: string, nullable
