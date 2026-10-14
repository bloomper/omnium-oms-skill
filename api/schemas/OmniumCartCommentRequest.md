# OmniumCartCommentRequest

Request model for adding a comment to a cart

**Fields:**

- `cartId`: string, nullable — Cart ID
- `commentType`: [`OmniumCommentType`](OmniumCommentType.md)
- `commentText`: string, nullable — The comment text/message content
- `createdBy`: string, nullable — The sender/creator of the comment (e.g., external user, system or sales channel). Defaults to the current's API user's username
- `tags`: List<string>, nullable — Tags for categorizing/filtering comments
- `marketId`: string, nullable — Market ID (optional, uses cart's market if not specified)
- `sendEmail`: boolean — Send email notification to customer
- `emailSubject`: string, nullable — Email subject line
- `emailTo`: string, nullable — Email recipient address. Falls back to cart's customer email if not specified.
- `emailCc`: string, nullable — Email CC recipients (semicolon separated)
- `emailBcc`: string, nullable — Email BCC recipients (semicolon separated)
- `emailFrom`: string, nullable — Email from address. Falls back to store's default email if not specified.
- `senderName`: string, nullable — Sender display name for email and SMS notifications. Falls back to store's default sender name if not specified.
- `sendSms`: boolean — Send SMS notification to customer
- `smsPhoneNumber`: string, nullable — Phone number for SMS. Falls back to cart's customer phone if not specified.

**Used directly by endpoints in:**

- [Carts | Other](../endpoints/carts/other.md)
