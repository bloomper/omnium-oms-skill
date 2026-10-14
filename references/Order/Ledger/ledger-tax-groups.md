# Tax Groups (VAT codes)

Source: https://docs.omnium.no/docs/Order/Ledger/ledger-tax-groups

> Tenant-managed VAT codes in Omnium — a rate, an ERP/SAF-T external code (MVA-kode), validity windows for rate changes, and how tax groups tag products, prices, and ledger lines.

A **tax group** is a tenant-defined VAT code: a short stable identifier carrying a VAT **rate** and an **external code** (the ERP / SAF-T MVA-kode) that your accounting system uses on a VAT return. Tax groups tag products, prices, and order lines, and that tag flows through to the VAT code and rate on each [ledger line](ledger-postings.md).

Tax groups are managed under **Configuration → Settings → Advanced → Accounting settings**, on the **VAT rates** tab.

The tax-group subsystem is an **independent switch** from posting generation. Tax groups tag products, prices, and order lines whether or not postings are enabled, and postings can be generated without tax groups.

## Why tax groups exist

A bare VAT rate (`25`) is not enough for accounting. Two lines can both be 0 % yet need different treatment — _zero-rated_ (within VAT scope, reported) versus _outside VAT scope_ (not reported as VAT at all). A tax group attaches the rate **and** the ERP code **and** the scope flag to a single tenant-chosen code, so the distinction survives all the way to the VAT return.

## Enable tax groups

| Setting | Description |
| --- | --- |
| Tax groups enabled | Turns tax groups on. When on, the default tax group selector and the tax-group list appear, and the tax-group **Code** field appears on products. |
| Default tax group | The code applied to a product, price, or line that has no group of its own — see [How a line gets its tax group](ledger-tax-groups.md#how-a-line-gets-its-tax-group) for the exceptions. Lists the active tax-group codes. Must reference an active group valid today. |

## Tax group fields

| Field | Required | Description |
| --- | --- | --- |
| Code | ✓ | Short stable identifier referenced by products, prices, and lines (e.g. `HIGH`, `FOOD`, `OUTSIDE`). Shown as **Code**. |
| Name | ✓ | Display name shown in the UI (e.g. `Standard 25%`, `Næringsmidler 15%`). |
| Description |  | Optional longer note. Not shown in the **VAT rates** list. |
| Rate | ✓ | VAT rate as a percentage — `25` for 25 %, `11.11` for 11.11 %, `0` for zero-rated. Shown as **Rate (%)**. A product with this tax group is sold at this rate. |
| External code |  | The ERP / SAF-T tax code (Norwegian **MVA-kode** such as `3`, `33`, `6`). Shown as **VAT code**. Free text — each ERP and country differs. Copied onto order lines with the group. On web-fulfillment postings, it becomes the VAT code on `OutputVat` lines whose order lines carry it, replacing the chart entry's VAT code. POS postings use the chart entry's VAT code, because the Z-report totals VAT by rate only. |
| Outside VAT scope |  | Mark groups that represent activity **outside** the VAT system (no VAT applies). Shown as **Outside VAT**. Validation then does not require an `OutputVat` account for the group, and does not report its 0 % rate. |
| Valid from / Valid to |  | Inclusive validity window (date only). Used to handle government rate changes — see below. |
| Active |  | Inactive groups are not selectable and are ignored when Omnium looks up a group by code, rate, or as the default. A group already stored on an order line is still used by web-fulfillment postings. Turn a group off instead of deleting it. Defaults to on. |

## Saving the VAT rates

When **Tax groups enabled** is on, the VAT rates are only valid when:

*   at least one tax group exists, and a default tax group is selected;
*   no rate is negative;
*   no **Valid from** is after its **Valid to**;
*   active entries with the same code do not have overlapping validity windows;
*   the default tax group is active and valid today.

Saving with the card's **Save** button is refused when the change breaks one of these rules. **Save and publish** saves without checking them; the settings are validated only afterwards, without blocking the save.

## Validity windows and rate changes

When a government changes a VAT rate, you do **not** edit the existing group in place. Instead, introduce a **new entry with the same code** and the new effective `Valid from` date, and set the old entry's `Valid to` to the day before. The group valid on the date of each sale or posting is used, so historical postings keep the rate that was in force when the sale happened and new postings pick up the new rate automatically.

Validity windows for the same code must **not overlap** — an overlap is ambiguous, and the card's **Save** refuses it. The **Validation** tab reports every repeated code as `Duplicate tax group code`, including a correct rate-change chain; you can ignore that message when the windows do not overlap.

## How a line gets its tax group

*   **Products and prices** — when a product or price is saved, the group comes from the price's own code, then the product's tax group (the **Code** field), and otherwise the default tax group. A product that has its own **Tax rate** but no tax group gets no group. The group's code and rate are stored on the price.
*   **Carts, and orders placed from a cart** — a line keeps the group it already has, and that group's code, external code, and rate are applied to the line. A line with an explicit tax rate but no group stays without a group; any other line gets the default tax group.
*   **In-Store app sales** — a line keeps the group it already has. A line without one gets the active group with the same rate on the sale date. When several groups share that rate (for example several 0 % groups) or none does, the default tax group is used only if its rate equals the line's rate; otherwise the line has no tax group.
*   **Web-fulfillment postings** — the line's stored group is used. A line without one gets the active group with the line's rate on the posting date, when exactly one group has that rate; otherwise it gets the default tax group, even when the default's rate differs from the line's.

POS postings are built from the Z-report, which totals sales and VAT by rate without tax groups. Their `Sales` and `OutputVat` lines are therefore matched to accounts by rate — see the note in [How an account is resolved](ledger-chart-of-accounts.md#how-an-account-is-resolved).

## Tax group on a product

When tax groups are enabled, a product's **Settings** tab shows a **Code** field in the **Tax** section — the product's tax group — listing the active tax-group codes. Leave it empty to use the default tax group — unless the product has its own **Tax rate**, in which case it gets no group; the placeholder names the default (`Default tax group: HIGH`). Choosing a group also sets the VAT rate the product is sold at.

## Presets

Omnium ships starter charts for common markets that you can apply and then adjust. The **Insert Norwegian preset**, **Insert Swedish preset**, and **Insert Danish preset** buttons are shown while the list is empty. **You must verify every external code against your own ERP's MVA-kode chart** — the presets use commonly-used codes but every ERP differs.

### Norway

| Code | Name | Rate | External code |
| --- | --- | --- | --- |
| `HIGH` | Standard 25% | 25 | `3` |
| `MID` | Lav sats 12% | 12 | `33` |
| `FOOD` | Næringsmidler 15% | 15 | `31` |
| `FISH` | Råfisk 11.11% | 11.11 | `32` |
| `ZERO` | Fritatt for mva (0%) | 0 | `5` |
| `EXPORT` | Eksport (0%) | 0 | `52` |
| `OUTSIDE` | Utenfor mva-loven | 0 (outside scope) | `6` |
| `PANT` | Pant (statsavgift) | 0 (outside scope) | — |

`PANT` represents a bottle/container deposit — a statutory deposit, not VAT — and should be mapped to its own GL account.

### Sweden

| Code | Name | Rate | External code |
| --- | --- | --- | --- |
| `HIGH` | Standard 25% | 25 | `MP1` |
| `MID` | Livsmedel 12% | 12 | `MP2` |
| `LOW` | Böcker, transport 6% | 6 | `MP3` |
| `ZERO` | Momsfri (0%) | 0 | `MF` |
| `EXPORT` | Export (0%) | 0 | `EX` |
| `EU_B2B` | EU-försäljning utan moms | 0 | `EUF` |
| `OUTSIDE` | Utanför momslagen | 0 (outside scope) | — |

### Denmark

| Code | Name | Rate | External code |
| --- | --- | --- | --- |
| `HIGH` | Moms 25% | 25 | `U25` |
| `ZERO` | Nulsats (0%) | 0 | `U0` |
| `EXPORT` | Eksport (0%) | 0 | `UEX` |
| `EU_B2B` | EU-salg uden moms | 0 | `UEU` |
| `OUTSIDE` | Uden for momsloven | 0 (outside scope) | — |

## Related pages

*   [Postings & Ledger Lines](ledger-postings.md#how-sales-and-vat-are-split) — how sales and VAT are split per rate and tagged with the group's code.
*   [Chart of Accounts](ledger-chart-of-accounts.md) — bind a chart entry to a tax group.
*   [Configuration](ledger-config.md) — the setup order and the Validation tab.
