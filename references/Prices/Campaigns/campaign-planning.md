# Campaign Planning

Source: https://docs.omnium.no/docs/Prices/Campaigns/campaign-planning

> How to set revenue targets and sales volume forecasts at the campaign, promotion, and SKU level for accurate campaign planning.

## Planning Overview

Campaign planning in Omnium supports revenue forecasting at three levels of detail:

| Level | Where targets are set | Granularity |
| --- | --- | --- |
| **Campaign** | Aggregated from linked promotions | Total campaign revenue and volume |
| **Promotion** | On the promotion itself (manually) | Revenue per promotion |
| **SKU** | On price list items | Revenue and volume per individual product/variant |

Planning targets are set during the **Planning** status. Once the campaign moves to **Active**, planning inputs are locked.

* * *

## SKU-Level Planning (Price List Promotions)

The most detailed planning is available for promotions that use price lists. Each price list item (representing a single SKU) can carry:

| Field | Description |
| --- | --- |
| **Planned Sales Volume** | Expected number of units to sell during the campaign |
| **Planned Revenue** | Automatically calculated from Planned Sales Volume x Discounted Price |
| **Planning Comment** | Internal note explaining the forecast rationale |

### Setting Up SKU-Level Targets

1.  Create a [price list](../PriceLists.md) with the products included in the campaign
2.  For each price list item, set the discounted price
3.  Enter the **Planned Sales Volume** — the expected number of units to sell
4.  The **Planned Revenue** is calculated automatically
5.  Optionally add a **Planning Comment** for context
6.  Link the price list promotion to the campaign

### Example

A clearance promotion for shoes includes a price list with three SKUs:

| SKU | Original Price | Discounted Price | Planned Volume | Planned Revenue |
| --- | --- | --- | --- | --- |
| SHOE-001 | 1,200 NOK | 840 NOK | 50 units | 42,000 NOK |
| SHOE-002 | 900 NOK | 630 NOK | 80 units | 50,400 NOK |
| SHOE-003 | 1,500 NOK | 1,050 NOK | 30 units | 31,500 NOK |
| **Total** |  |  | **160 units** | **123,900 NOK** |

These targets roll up to the promotion level and then to the campaign level, giving you a full picture of the expected campaign outcome.

* * *

## Promotion-Level Planning

For promotions that don't use price lists (category/brand, multi-buy, order amount, etc.), you can set a **Planned Revenue** estimate directly on the promotion. This is a manual estimate since these promotion types apply dynamically based on cart contents.

Each promotion also supports a **Planning Notes** field for documenting assumptions, market context, or other planning considerations.

* * *

## Campaign-Level Aggregation

Campaign-level targets are automatically aggregated from the linked promotions:

*   **Total Planned Revenue** = Sum of all linked promotions' planned revenue (both SKU-level and promotion-level)
*   **Total Planned Sales Volume** = Sum of all SKU-level planned volumes from price list promotions

This aggregation provides a single view of the campaign's expected performance without manual data entry at the campaign level.

* * *

## Planning Notes

Campaigns have a dedicated **Planning Notes** field for documenting:

*   Campaign goals and strategy
*   Target audience and market context
*   Key assumptions behind revenue forecasts
*   Dependencies or risks
*   Coordination notes with other teams

Planning notes are visible in the campaign detail view and are preserved throughout the campaign lifecycle.

* * *

## Planning Best Practices

### Be conservative with volume estimates

Use historical sales data from similar campaigns as a baseline. If no prior data exists, start with moderate estimates and adjust after the first campaign cycle.

### Use tags to categorize

Tag campaigns by type (e.g., `seasonal`, `clearance`, `product-launch`) to build up historical benchmarks over time. Compare campaigns with the same tags to refine future forecasts.

### Plan at the SKU level when possible

Price list promotions with SKU-level targets give you the most accurate forecasts and the most actionable post-campaign insights. Use this level of detail for your most important campaigns.

### Document assumptions

Use planning notes and planning comments to explain why specific targets were set. This context is valuable when reviewing campaign performance after the fact.
