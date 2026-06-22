# Products API

GoHighLevel Products API reference — product catalog, pricing/variants, inventory, collections, reviews, and store management. **27 endpoints** across 5 sub-groups under the `/products/` path prefix.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Files in this folder

| File | Covers | Endpoints |
|------|--------|-----------|
| [schemas.md](./schemas.md) | Overview, required scopes, authentication, rate limits, all object schemas (Product, Price, Collection, Review, Inventory Record), error responses, general notes/gotchas | — (reference) |
| [products-core.md](./products-core.md) | Product CRUD (create, list, get, update, delete) plus Bulk Update Products and Bulk Edit Products and Prices | 7 |
| [prices-inventory.md](./prices-inventory.md) | Price/variant CRUD (create, list, get, update, delete) plus List Inventory and Update Inventory | 7 |
| [collections.md](./collections.md) | Collection/category CRUD (list, create, get, update, delete) | 5 |
| [reviews-store.md](./reviews-store.md) | Reviews (list, bulk update, count, update, delete) plus Store (include/exclude, display priorities, stats) | 8 |

## Quick routing

- **Creating / listing / editing products, bulk catalog operations** → `products-core.md`
- **Prices, variants, recurring billing config, stock/inventory levels** → `prices-inventory.md`
- **Organizing products into collections/categories** → `collections.md`
- **Moderating reviews or managing what shows in a store** → `reviews-store.md`
- **Field names, object shapes, scopes, auth, error codes, casing gotchas** → `schemas.md`

## Key gotchas (see schemas.md for full list)

- **`total` is an array** on List Products: `[{"total": N}]`, not a plain number.
- **Collection `altType` casing:** uppercase `LOCATION` for Create/Update, lowercase `location` for List/Delete.
- **`productId` accepts a slug** on Get/Update/Delete Product, not just the MongoDB ID.
- **Bulk Update vs Bulk Edit:** Bulk Update operates on product-level attributes across many products; Bulk Edit inline-edits individual product and price fields.

> **Agent Note:** Documentation created from OpenAPI spec research. All endpoints are 🔬 unverified until tested against a live GHL API. If you use any endpoint and find errors, fix the relevant file and update the [master index](../../index.md).
