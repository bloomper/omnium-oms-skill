# OmniumEventSubscription

A subscription that forwards Omnium events to an external destination - a webhook endpoint,
a queue or a message bus. Equivalent to what the Event Subscriptions page in the Omnium UI manages.

**Fields:**

- `id`: string, nullable — Unique identifier. Assigned by Omnium when omitted on create; supply your own to keep ids stable across environments.
- `name`: string, required, max length 200 — Name shown in the UI and included in delivery log entries.
- `isActive`: boolean — When false the subscription is kept but no events are delivered.
- `connector`: string, required, max length 100 — Delivery mechanism. Built-in values are Webhook, AzureServiceBus, AzureStorageQueue, Kafka and RabbitMQ; installed integration modules may add more.
- `baseUrl`: string, nullable, max length 2000 — Target host for HTTP delivery, for example https://example.com. Required by the Webhook connector.
- `requestUri`: string, nullable, max length 2000 — Path appended to Omnium.Public.Events.Models.OmniumEventSubscription.BaseUrl, for example /hooks/orders.
- `httpVerb`: string, nullable, max length 10 — HTTP method used for delivery, for example POST.
- `topic`: string, nullable, max length 500 — Queue, topic or stream name. Used by the queue and bus connectors instead of a URL.
- `clientId`: string, nullable, max length 500 — Client id used to authenticate against the destination.
- `clientSecret`: string, nullable, max length 2000 — Client secret or connection string used to authenticate against the destination. Write-only: always returned as null. Omit it on update to keep the stored value, or send an empty string to clear it.
- `messageEncoding`: string, nullable, max length 50 — Character encoding of the delivered message body, for example UTF-8.
- `requestHeaders`: List<[`OmniumPropertyItem`](OmniumPropertyItem.md)>, nullable — Extra HTTP headers sent with each delivery. Values of credential-carrying headers (authorization, api key, token, secret, password and cookie headers) are returned as null; leave such a value null on update to keep the stored one, or send an empty string to clear it.
- `useQueueDelivery`: boolean — When true, events are put on an internal queue and delivered asynchronously with retries instead of being sent inline.
- `filters`: [`OmniumEventSubscriptionFilters`](OmniumEventSubscriptionFilters.md)

**Used directly by endpoints in:**

- [Settings | Event Subscriptions](../endpoints/settings/event-subscriptions.md)
