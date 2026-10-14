# OmniumAccountingLedgerLine

A single line on an accounting posting (one debit/credit entry for one account).

**Fields:**

- `id`: string, nullable — Unique ledger line ID. Set by Omnium.
- `postingId`: string, nullable — ID of the parent posting this line belongs to.
- `lineSequence`: integer (int32) — Order within the parent posting. Stable; used for sorting and hash determinism.
- `postingDate`: string (date-time), nullable — Accounting period date, denormalised from the parent posting.
- `registerId`: string, nullable — Register that produced this line, when applicable (POS).
- `storeId`: string, nullable — Store this line is booked to.
- `accountingCompanyCode`: string, nullable — Accounting company code (when configured per store/market).
- `accountNumber`: string, nullable — Account number this entry is booked to.
- `accountName`: string, nullable — Human-readable account name.
- `category`: [`OmniumAccountCategory`](OmniumAccountCategory.md)
- `debit`: number (decimal) — Debit amount in posting currency.
- `credit`: number (decimal) — Credit amount in posting currency.
- `currencyCode`: string, nullable — ISO currency code for the amount (e.g. NOK, SEK, EUR).
- `vatCode`: string, nullable — VAT code (when applicable, on VAT-related lines).
- `vatRate`: number (decimal), nullable — VAT rate as a percentage (e.g. 25 for 25%).
- `vatAmount`: number (decimal), nullable — VAT amount in posting currency.
- `taxGroupCode`: string, nullable — Tax-group code (from line-level snapshot).
- `description`: string, nullable — Free-text description for the line.
- `customerNumber`: string, nullable — Customer number (when applicable, e.g. for receivables).
- `invoiceNumber`: string, nullable — Invoice number (when applicable).
- `creditNoteNumber`: string, nullable — Credit note number (when applicable).
- `invoiceDate`: string (date-time), nullable — Invoice date (when applicable).
- `dueDate`: string (date-time), nullable — Due date (when applicable).
- `kidNumber`: string, nullable — KID / payment-reference number (when applicable).
- `dimensions`: Dictionary<string, string>, nullable — Free-form dimensions / cost centres carried on the line.

**Used directly by endpoints in:**

- [Accounting | Ledger Lines](../endpoints/accounting/ledger-lines.md)
