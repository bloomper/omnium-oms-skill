# API Reference

Source: https://docs.omnium.no/docs/Prices/Campaigns/campaign-api

> Technical guide to the Campaign public REST API — create, update, search, and manage campaign lifecycles programmatically via the Omnium API.

## Overview

The Campaign API lets external systems manage campaigns programmatically. Use it to integrate campaign management with your ERP, build custom campaign dashboards, or automate campaign workflows from external systems.

All endpoints are available at `https://apitest.omnium.no/api/Campaigns` (test) and your production API host under the same path.

### What you can do

*   Create and update campaigns with markets, stores, tags, and custom properties
*   Search and filter campaigns by date range, status, market, and more
*   Transition campaigns through their lifecycle (Planning → Active → Completed → Archived)
*   Retrieve promotion IDs linked to a campaign
*   Delete campaigns (with automatic promotion unlinking)

### What the Campaign API does not do

Campaign API manages the campaign container itself. To manage the promotions within a campaign, use the [Promotions API](../Promotions.md). To set SKU-level pricing, use the [Price Lists API](../PriceLists.md). The relationship looks like this:

```
  ┌──────────────────────────────────────┐
  │          Campaign API                │  ← You are here
  │   POST /api/Campaigns                │
  │   Create, update, search, delete     │
  └──────────────┬───────────────────────┘
                 │
                 │  GET /api/Campaigns/{id}/promotions
                 │  (returns promotion IDs)
                 ▼
  ┌──────────────────────────────────────┐
  │         Promotions API               │
  │   POST /api/Promotions               │
  │   Define discount rules              │
  └──────────────┬───────────────────────┘
                 │
                 ▼
  ┌──────────────────────────────────────┐
  │        Price Lists API               │
  │   SKU-level prices and planning      │
  └──────────────────────────────────────┘
```

* * *

## Authentication and Authorization

All Campaign endpoints require a valid JWT Bearer token. Include it in the `Authorization` header:

```
Authorization: Bearer <your-token>
```

Obtain a token via `POST /api/Token`. See [Authentication](../../GettingStartedWithAPI/introtoapi.md#authentication) for details.

### Access Levels

| Operation | Required Role | Description |
| --- | --- | --- |
| Read (GET, Search) | `PromotionRead` | View campaigns and linked promotions |
| Write (POST, PUT, PATCH, DELETE) | `PromotionAdmin` | Create, update, delete, and change status |

* * *

## Campaign Model

The API returns campaigns using the `OmniumCampaign` model.

| Property | Type | Description |
| --- | --- | --- |
| `Id` | `string` | Unique campaign identifier (GUID). Auto-generated on create if not provided. |
| `Name` | `string` | Campaign display name. **Required.** |
| `Description` | `string` | Optional internal description. |
| `Tags` | `string[]` | Labels for categorization and filtering (e.g., `["seasonal", "clearance"]`). |
| `StartDate` | `datetime` | When the campaign begins. **Required.** |
| `EndDate` | `datetime` | When the campaign ends. Must be after `StartDate`. **Required.** |
| `Status` | `string` | Lifecycle status: `Planning`, `Active`, `Completed`, or `Archived`. |
| `Markets` | `string[]` | Market IDs where the campaign applies (e.g., `["NOR", "SWE"]`). |
| `Stores` | `string[]` | Store IDs where the campaign targets. Empty means all stores. |
| `PlanningNotes` | `string` | Internal notes for the planning phase. |
| `CreatedDate` | `datetime?` | When the campaign was created. Read-only (set by server). |
| `UpdatedDate` | `datetime?` | When the campaign was last updated. Read-only (set by server). |
| `Properties` | `OmniumPropertyItem[]` | Custom key-value pairs for extensibility. |
| `Errors` | `OmniumEntityError[]` | Validation or processing errors. Read-only (set by server). |

### OmniumPropertyItem

| Property | Type | Description |
| --- | --- | --- |
| `Key` | `string` | Property name |
| `Value` | `string` | Property value |
| `KeyGroup` | `string` | Optional grouping key |
| `ValueType` | `string` | Optional type hint (e.g., `"string"`, `"number"`, `"boolean"`) |

* * *

## Endpoints

### Get a Campaign

```
GET /api/Campaigns/{id}
```

Returns a single campaign by ID.

**Response:** `200 OK` with `OmniumCampaign` body, or `404 Not Found`.

**Example:**

```
curl -X GET "https://apitest.omnium.no/api/Campaigns/abc-123" \
  -H "Authorization: Bearer <token>"
```

```json
{
  "id": "abc-123",
  "name": "Summer Sale 2026",
  "description": "Annual summer clearance across all markets",
  "tags": ["seasonal", "clearance"],
  "startDate": "2026-06-01T00:00:00",
  "endDate": "2026-08-31T23:59:59",
  "status": "Planning",
  "markets": ["NOR", "SWE"],
  "stores": [],
  "planningNotes": "Focus on shoe and clothing categories. Target 500k NOK.",
  "createdDate": "2026-03-15T10:30:00Z",
  "updatedDate": "2026-03-18T14:22:00Z",
  "properties": [],
  "errors": []
}
```

* * *

### Search Campaigns

```
POST /api/Campaigns/Search
```

Search and filter campaigns with pagination. This is the primary way to list campaigns — there is no `GET /api/Campaigns` list endpoint.

**Request body:** `OmniumCampaignSearchRequest`

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `SearchText` | `string` | — | Free-text search across name, description, tags, and ID. Supports fuzzy matching. |
| `StartFrom` | `datetime?` | — | Campaigns that start on or after this date |
| `StartTo` | `datetime?` | — | Campaigns that start on or before this date |
| `EndFrom` | `datetime?` | — | Campaigns that end on or after this date |
| `EndTo` | `datetime?` | — | Campaigns that end on or before this date |
| `Statuses` | `string[]` | — | Filter by statuses. Empty means all. Values: `Planning`, `Active`, `Completed`, `Archived` |
| `MarketIds` | `string[]` | — | Filter by market IDs |
| `StoreIds` | `string[]` | — | Filter by store IDs |
| `Tags` | `string[]` | — | Filter by tags |
| `CampaignIds` | `string[]` | — | Filter by specific campaign IDs |
| `Page` | `int` | `1` | Page number (1-based) |
| `Take` | `int` | `20` | Results per page. **Max 200.** |
| `SortOrder` | `string` | `StartDateDesc` | Sort order (see table below) |
| `ExcludeArchived` | `bool` | `true` | Exclude archived campaigns from results |

**Sort options:**

| Value | Description |
| --- | --- |
| `StartDateDesc` | Newest start date first (default) |
| `StartDateAsc` | Oldest start date first |
| `EndDateDesc` | Newest end date first |
| `EndDateAsc` | Oldest end date first |
| `NameAsc` | Alphabetical A-Z |
| `NameDesc` | Alphabetical Z-A |
| `CreatedAsc` | Oldest created first |
| `CreatedDesc` | Newest created first |

**Pagination limits:** `Take` cannot exceed 200. `Take × Page` cannot exceed 10,000. For larger datasets, use narrower filters.

**Response:** `200 OK` with `OmniumSearchResult<OmniumCampaign>`:

```json
{
  "result": [ ... ],
  "totalHits": 42
}
```

**Example — Find all active campaigns in the Norwegian market:**

```
curl -X POST "https://apitest.omnium.no/api/Campaigns/Search" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "statuses": ["Active"],
    "marketIds": ["NOR"],
    "page": 1,
    "take": 20
  }'
```

**Example — Find campaigns running during a specific week:**

To find campaigns that overlap with a date range, combine `StartTo` and `EndFrom`. This returns campaigns where `StartDate <= endOfRange` AND `EndDate >= startOfRange`:

```
curl -X POST "https://apitest.omnium.no/api/Campaigns/Search" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "startTo": "2026-07-07T23:59:59",
    "endFrom": "2026-07-01T00:00:00",
    "page": 1,
    "take": 50
  }'
```

**Example — Full-text search with tag filter:**

```
curl -X POST "https://apitest.omnium.no/api/Campaigns/Search" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "searchText": "summer",
    "tags": ["seasonal"],
    "excludeArchived": true
  }'
```

* * *

### Create a Campaign

```
POST /api/Campaigns
```

Creates a new campaign. Returns the created campaign with server-set fields (`Id`, `CreatedDate`, `Status`).

**Request body:** `OmniumCampaignRequest`

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| `Id` | `string` | No | Custom ID. A GUID is generated if omitted. |
| `Name` | `string` | **Yes** | Campaign display name |
| `Description` | `string` | No | Internal description |
| `Tags` | `string[]` | No | Categorization labels |
| `StartDate` | `datetime` | **Yes** | Campaign start date |
| `EndDate` | `datetime` | **Yes** | Campaign end date (must be after `StartDate`) |
| `Status` | `string` | No | Initial status. Defaults to `Planning` if omitted. Must be a valid status. |
| `Markets` | `string[]` | No | Market IDs |
| `Stores` | `string[]` | No | Store IDs |
| `PlanningNotes` | `string` | No | Internal planning notes |
| `Properties` | `OmniumPropertyItem[]` | No | Custom key-value pairs |

**Response:** `201 Created` with the full `OmniumCampaign` body.

**Validation rules:**

*   `Name` must not be empty
*   `EndDate` must be after `StartDate`
*   `Status` (if provided) must be one of: `Planning`, `Active`, `Completed`, `Archived`

**Example — Create a basic campaign:**

```
curl -X POST "https://apitest.omnium.no/api/Campaigns" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Black Friday 2026",
    "description": "Annual Black Friday campaign across all channels",
    "startDate": "2026-11-20T00:00:00",
    "endDate": "2026-11-30T23:59:59",
    "markets": ["NOR", "SWE", "DNK"],
    "tags": ["black-friday", "seasonal"],
    "planningNotes": "Coordinate with marketing team. Budget: 200k NOK."
  }'
```

**Example — Create with custom properties:**

Properties let you attach arbitrary metadata to campaigns. Use them for ERP references, budget tracking, or any custom data:

```
curl -X POST "https://apitest.omnium.no/api/Campaigns" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Spring Collection Launch",
    "startDate": "2026-03-15T00:00:00",
    "endDate": "2026-04-15T23:59:59",
    "markets": ["NOR"],
    "properties": [
      { "key": "ErpCampaignId", "value": "CAMP-2026-042", "keyGroup": "ERP" },
      { "key": "Budget", "value": "150000", "valueType": "number", "keyGroup": "Finance" }
    ]
  }'
```

* * *

### Update a Campaign

```
PUT /api/Campaigns/{id}
```

Replaces an existing campaign with the provided data. This is a full replacement — all fields in the request body overwrite the existing values. Fields you omit will be cleared (except server-managed fields like `CreatedDate`).

**Request body:** Same as Create (`OmniumCampaignRequest`).

**Response:** `200 OK` with updated `OmniumCampaign`, or `404 Not Found`.

**Important behavior:**

*   The `Id` in the URL takes precedence over any `Id` in the request body
*   `CreatedDate` is preserved from the original campaign
*   If `Status` is omitted or empty, the existing status is preserved
*   Server-managed fields (such as `CreatedDate`, `Errors`) are preserved

**Example — Update campaign dates and add a store filter:**

```
curl -X PUT "https://apitest.omnium.no/api/Campaigns/abc-123" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Summer Sale 2026",
    "description": "Extended to include August",
    "startDate": "2026-06-01T00:00:00",
    "endDate": "2026-08-31T23:59:59",
    "markets": ["NOR", "SWE"],
    "stores": ["store-oslo", "store-stockholm"],
    "tags": ["seasonal", "clearance"]
  }'
```

> Since PUT is a full replacement, include all fields you want to keep. For example, if the campaign had `Tags` and you omit `Tags` in the PUT request, the tags will be cleared.

* * *

### Delete a Campaign

```
DELETE /api/Campaigns/{id}
```

Permanently deletes a campaign. All promotions linked to the campaign are automatically unlinked (but not deleted).

**Response:** `204 No Content` on success, or `404 Not Found`.

**Example:**

```
curl -X DELETE "https://apitest.omnium.no/api/Campaigns/abc-123" \
  -H "Authorization: Bearer <token>"
```

> Deletion is permanent. Campaign performance data is also removed. Linked promotions continue to function independently after being unlinked.

* * *

### Update Campaign Status

```
PATCH /api/Campaigns/{id}/status
```

Transitions a campaign to a new lifecycle status. This is the recommended way to change status — it triggers any configured workflow actions (such as notifications) and validates the transition.

**Request body:**

```json
{
  "status": "Active"
}
```

| Property | Type | Required | Description |
| --- | --- | --- | --- |
| `status` | `string` | **Yes** | Target status: `Planning`, `Active`, `Completed`, `Archived` |

**Response:** `200 OK` with updated `OmniumCampaign`, `404 Not Found`, or `400 Bad Request` (invalid status value).

**Status transition rules:**

```
  Planning  ──→  Active  ──→  Completed  ──→  Archived
                   │
                   └──→  Planning  (rollback)
```

| From | Allowed Transitions |
| --- | --- |
| `Planning` | → `Active` |
| `Active` | → `Completed`, → `Planning` (rollback) |
| `Completed` | → `Archived` |
| `Archived` | (no transitions) |

> Your Omnium environment may be configured with additional statuses and transitions. Use `Planning → Active → Completed → Archived` as the standard flow.

**What happens on status change:**

When you change a campaign's status, the system may execute configured workflow steps — for example, sending notification emails or SMS messages to relevant stakeholders. The workflow is configured per status in your environment settings.

**Example — Activate a campaign:**

```
curl -X PATCH "https://apitest.omnium.no/api/Campaigns/abc-123/status" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{ "status": "Active" }'
```

**Example — Complete a campaign after its end date:**

```
curl -X PATCH "https://apitest.omnium.no/api/Campaigns/abc-123/status" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{ "status": "Completed" }'
```

* * *

### Get Linked Promotions

```
GET /api/Campaigns/{id}/promotions
```

Returns the IDs of all promotions linked to a campaign. Use these IDs with the Promotions API to retrieve full promotion details.

**Response:** `200 OK` with `string[]`, or `404 Not Found`.

**Example:**

```
curl -X GET "https://apitest.omnium.no/api/Campaigns/abc-123/promotions" \
  -H "Authorization: Bearer <token>"
```

```json
["promo-001", "promo-002", "promo-003"]
```

> Promotion linking and unlinking is currently done through the Omnium UI. The public API provides read access to the linked promotion IDs.

* * *

## Common Integration Patterns

### ERP Campaign Sync

A typical ERP integration creates campaigns in Omnium to match campaign plans from the ERP, then tracks performance back:

```
  ┌────────────┐        ┌───────────────┐
  │    ERP     │───────►│  Omnium API   │
  │  (source)  │ Create │               │
  └────────────┘        └───────┬───────┘
                                │
                         Campaign created
                         (Status: Planning)
                                │
                                ▼
                        ┌───────────────┐
                        │  Omnium UI    │
                        │  Link promos  │
                        │  Set targets  │
                        └───────┬───────┘
                                │
                          Status → Active
                                │
                                ▼
  ┌────────────┐        ┌───────────────┐
  │    ERP     │◄───────│  Omnium API   │
  │  (target)  │ Search │               │
  └────────────┘ + Get  └───────────────┘
```

**Step 1:** ERP creates the campaign via `POST /api/Campaigns` with an ERP reference in `Properties`:

```json
{
  "name": "Q2 Clearance",
  "startDate": "2026-04-01T00:00:00",
  "endDate": "2026-06-30T23:59:59",
  "markets": ["NOR"],
  "properties": [
    { "key": "ErpCampaignId", "value": "ERP-2026-Q2-CLR", "keyGroup": "ERP" }
  ]
}
```

**Step 2:** Merchandising team links promotions and sets targets in the Omnium UI.

**Step 3:** ERP activates the campaign via `PATCH /api/Campaigns/{id}/status`.

**Step 4:** ERP periodically searches for active campaigns to pull status updates:

```json
{
  "statuses": ["Active"],
  "marketIds": ["NOR"],
  "sortOrder": "EndDateAsc"
}
```

### Custom Dashboard

Build a campaign calendar or dashboard by searching for campaigns across a date range:

```json
{
  "startTo": "2026-12-31T23:59:59",
  "endFrom": "2026-01-01T00:00:00",
  "excludeArchived": true,
  "take": 200,
  "sortOrder": "StartDateAsc"
}
```

This returns all campaigns that overlap with the year 2026, sorted chronologically.

### Automated Lifecycle Management

Use the status endpoint to automate campaign transitions based on business rules:

1.  **Activate on start date:** A scheduled job searches for `Planning` campaigns where `StartDate` has passed, then calls `PATCH /status` with `Active`
2.  **Complete on end date:** Search for `Active` campaigns where `EndDate` has passed, then transition to `Completed`
3.  **Archive after review period:** Search for `Completed` campaigns older than 90 days, then archive

* * *

## Error Handling

All endpoints follow a consistent error pattern:

| Status Code | Meaning | When |
| --- | --- | --- |
| `200 OK` | Success | GET, PUT, PATCH |
| `201 Created` | Resource created | POST (create) |
| `204 No Content` | Deleted successfully | DELETE |
| `400 Bad Request` | Validation error | Missing `Name`, invalid `Status`, `EndDate` before `StartDate`, pagination limits exceeded |
| `404 Not Found` | Campaign not found | GET, PUT, PATCH, DELETE with non-existent ID |
| `401 Unauthorized` | Missing or invalid token | Any endpoint |
| `403 Forbidden` | Insufficient permissions | Write operations without `PromotionAdmin` role |
| `500 Internal Server Error` | Server error | Unexpected failures |

**400 errors** return a descriptive message in the response body:

```
"EndDate must be after StartDate"
```

```
"Invalid status. Valid values: Planning, Active, Completed, Archived"
```

```
"Take cannot exceed 200"
```

* * *

## Endpoint Summary

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| `GET` | `/api/Campaigns/{id}` | Read | Get a single campaign |
| `POST` | `/api/Campaigns/Search` | Read | Search with filters and pagination |
| `POST` | `/api/Campaigns` | Admin | Create a new campaign |
| `PUT` | `/api/Campaigns/{id}` | Admin | Full update of an existing campaign |
| `DELETE` | `/api/Campaigns/{id}` | Admin | Delete a campaign |
| `PATCH` | `/api/Campaigns/{id}/status` | Admin | Change campaign status |
| `GET` | `/api/Campaigns/{id}/promotions` | Read | Get linked promotion IDs |
