# Updates

Source: https://docs.omnium.no/docs/Product/api-reference/product-updates

> Outlines best practices when updating products in Omnium via API

## Best practice

This guide outlines best practices when pushing product data to Omnium, focusing on efficient synchronization and asset handling.

### Product Synchronization

*   Use [`PatchMany`](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Batch%20Updates/ProductsPatchMany) whenever possible.

    *   Reduces system load and ensures that only relevant properties are updated.
    *   Ideal for continuous synchronization of specific changes to selected products.
*   Use [`UpdateMany`](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Batch%20Updates/ProductsUpdateMany) for full product catalog syncs (e.g., nightly or weekly).

    *   Updates only products that have changed.
    *   Improves performance and scalability.
*   Avoid using [`AddMany`](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Batch%20Updates/ProductsAddMany) except for an **initial population** of products.

## Product updates should be synchronous

Treat a `Product` as one atomic object. Variants, properties, prices, assets and other substructures are part of the same product. To avoid race conditions and partial updates, group related product changes into a single synchronous update per product.

### Why this matters

A product in Omnium is stored and handled as a single logical object. If you split related changes across multiple asynchronous calls, concurrent writers can overwrite each other or read intermediate states. Typical symptoms include missing variants, reverted property patches, or inconsistent data until all updates are complete.

### Recommendations

*   **Group related changes**: Combine related mutations (variants, properties, prices) into a single update so the product remains consistent.
*   **Batch update products if possible**: Use batch endpoints (`PatchMany`, `UpdateMany`) to maximize performance and keep synchronization efficient.
*   **Consider using a queue**: For event-driven systems that emit many small changes without guaranteed FIFO ordering, handle this by buffering and serializing updates per product through a queue or messaging system before sending them to Omnium.

* * *

### Handling Assets

When performing full product syncs (e.g., nightly/weekly), it's important to control asset updates carefully to avoid unintended uploads.

*   Use [`UpdateMany`](https://api.omnium.no/documentation/index.html#/Products%20%7C%20Batch%20Updates/ProductsUpdateMany) for asset-related updates.
*   Ensure that `ForceUpload` is set to `false`.
*   Only change the `ExternalUrl` of an asset when the underlying image has actually changed.

* * *
