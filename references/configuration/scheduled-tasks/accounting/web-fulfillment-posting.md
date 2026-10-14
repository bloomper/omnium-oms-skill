# Web Fulfillment Posting

Source: https://docs.omnium.no/docs/configuration/scheduled-tasks/accounting/web-fulfillment-posting

> Generate the daily web-fulfillment accounting postings for every store from the configured cutoff hour

## Overview

This task generates [web-fulfillment postings](../../../Order/Ledger/ledger-postings.md#posting-sources): balanced accounting vouchers for the payment transactions of web and click & collect orders. Each run checks the time in the accounting time zone; at or after the configured cutoff hour, it posts the **previous day** for every store, booking only the transactions that are not already on a posting.

## Identifier

| Property | Value |
| --- | --- |
| **Implementation Type** | `WebFulfillmentPostingScheduledTask` |
| **Group** | Accounting |
| **Type** | Delta |

* * *

## When to Use

Enable this task when you need:

*   Daily accounting postings for web and click & collect orders
*   Postings ready for export to your ERP or accounting system each morning
*   Late payment transactions for the previous day picked up automatically

POS sales do not need this task: their postings are built when a register's Z-report is closed.

* * *

## Configuration Properties

This task has no properties of its own. It is controlled by the tenant's [accounting settings](../../../Order/Ledger/ledger-config.md):

| Setting | Location | Description |
| --- | --- | --- |
| `postingEnabled` | `accountingSettings.postingEnabled` | Master switch. When off, the task does nothing. |
| `webFulfillmentPostingEnabled` | `accountingSettings.webFulfillmentPostingEnabled` | Must be on for the task to post. |
| `webFulfillmentPostingCutoffHour` | `accountingSettings.webFulfillmentPostingCutoffHour` | Hour (0–23, default `2`) in the accounting time zone. Before this hour, the task does nothing. |
| `timeZone` | `accountingSettings.timeZone` | IANA time zone used for the cutoff check and the day boundaries. Falls back to the tenant's time zone. |
| `webFulfillmentPostingPerOrderType` | `accountingSettings.webFulfillmentPostingPerOrderType` | When on, one posting per order type per store and day. |
| `postingExclusions` | `accountingSettings.postingExclusions` | [Rules](../../../Order/Ledger/ledger-posting-exclusions.md) that keep matching orders out of the postings. |

* * *

## Behavior

### What It Does

1.  Completes without doing anything unless both `postingEnabled` and `webFulfillmentPostingEnabled` are on
2.  Reads the current hour in the accounting time zone; if it is before the cutoff hour, completes without doing anything
3.  Otherwise selects the previous day and processes every store
4.  For each store (and order type, when per-order-type posting is on), books the day's captures and refunds that are not already on a posting, applying the posting exclusions
5.  Skips a store and day with nothing new to book
6.  Logs a store and day whose posting fails, and continues with the next one

### Prerequisites

*   `accountingSettings.postingEnabled` and `accountingSettings.webFulfillmentPostingEnabled` must be on
*   A chart of accounts, including a `Receivable` account for auto-balancing — see [Chart of Accounts](../../../Order/Ledger/ledger-chart-of-accounts.md)

### Side Effects

*   Creates accounting postings with source `WebFulfillment`
*   A run after a day was already posted books any transactions that arrived since, on an additional posting for that store and day
*   Never posts days before the previous day. To post older days, use **Generate web fulfillment postings** under **Reports → Accounting** — see [Postings in the UI](../../../Order/Ledger/ledger-reports.md#re-running-web-fulfillment)

* * *

## Example Configuration

First, ensure tenant settings enable posting:

```json
{
    "accountingSettings": {
        "postingEnabled": true,
        "webFulfillmentPostingEnabled": true,
        "webFulfillmentPostingCutoffHour": 2,
        "timeZone": "Europe/Oslo"
    }
}
```

Then configure the scheduled task:

```json
{
    "ImplementationType": "WebFulfillmentPostingScheduledTask",
    "Schedule": "0 * * * *",
    "IsDisabled": false
}
```

* * *

## Recommended Schedule

Run every hour (`0 * * * *`). The first run after the cutoff hour posts the previous day; later runs that day only book transactions that arrived since, and complete without changes otherwise.

* * *

## Related Tasks

*   [Orphaned Posting Cleanup](orphaned-posting-cleanup.md) - Remove draft postings left by an interrupted save
