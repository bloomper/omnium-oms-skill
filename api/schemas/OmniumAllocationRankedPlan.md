# OmniumAllocationRankedPlan

A plan's standing under one profile.

**Fields:**

- `planId`: string, nullable
- `score`: number (decimal) — Lower is better
- `labels`: List<string>, nullable, max length 10 — What this plan is best at, for showing a customer: Recommended, FewestShipments, Cheapest, Closest or MostLikelyToDeliver
- `breakdown`: Dictionary<string, number (decimal)>, nullable — Score per objective, for explaining the order
