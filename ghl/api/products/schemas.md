# Products API — Overview & Schemas

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Products API manages the full product catalog lifecycle within GoHighLevel, including product CRUD operations, pricing and variant management, inventory tracking, collection/category organization, product reviews, and store inclusion management. The API is organized into 5 sub-groups: Products (Core), Prices, Collections, Reviews, and Store, totaling 27 endpoints.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `products.readonly` | Read | List/get products, fetch reviews, review count, store stats |
| `products.write` | Read/Write | Create/update/delete products, bulk update, review CRUD, store include/exclude |
| `products/prices.readonly` | Read | List prices, get price, list inventory |
| `products/prices.write` | Read/Write | Create/update/delete price, update inventory |
| `products/collection.readonly` | Read | List/get collections |
| `products/collection.write` | Read/Write | Create/update/delete collections |

---

## Authentication

All requests require:

```
Authorization: Bearer {access_token}
Version: 2021-07-28
```

---

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

---

## Object Schemas

### Product Object

```json
{
  "_id": "00000000000000product0001",
  "name": "Premium T-Shirt",
  "description": "High-quality cotton t-shirt available in multiple sizes.",
  "productType": "PHYSICAL",
  "locationId": "EXAMPLELocation00000",
  "image": "https://storage.googleapis.com/example/tshirt.png",
  "statementDescriptor": "MYBRAND TSHIRT",
  "availableInStore": true,
  "variants": [
    {
      "id": "var_size_001",
      "name": "Size",
      "options": [
        { "id": "opt_s", "name": "S" },
        { "id": "opt_m", "name": "M" },
        { "id": "opt_l", "name": "L" },
        { "id": "opt_xl", "name": "XL" }
      ]
    }
  ],
  "medias": [
    {
      "id": "media_001",
      "title": "Front View",
      "url": "https://storage.googleapis.com/example/tshirt-front.png",
      "type": "image",
      "isFeatured": true,
      "priceIds": []
    }
  ],
  "collectionIds": ["0000000000000invoice0001"],
  "isTaxesEnabled": false,
  "taxes": [],
  "automaticTaxCategoryId": null,
  "taxInclusive": false,
  "isLabelEnabled": true,
  "label": {
    "title": "New Arrival",
    "startDate": "2024-01-01T00:00:00.000Z",
    "endDate": "2024-03-31T23:59:59.000Z"
  },
  "slug": "premium-t-shirt",
  "seo": {
    "title": "Premium T-Shirt - Best Quality",
    "description": "Shop our premium cotton t-shirt in S, M, L, XL."
  },
  "createdAt": "2024-01-23T10:30:00.000Z",
  "updatedAt": "2024-01-23T10:30:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | MongoDB ObjectId — unique product identifier |
| `name` | string | Product name |
| `description` | string | Product description (HTML or plain text) |
| `productType` | string | One of: `DIGITAL`, `PHYSICAL`, `SERVICE`, `PHYSICAL/DIGITAL` |
| `locationId` | string | Location (sub-account) this product belongs to |
| `image` | string | Primary product image URL |
| `statementDescriptor` | string | Appears on customer bank/card statements (max ~22 chars) |
| `availableInStore` | boolean | Whether product is visible in online store |
| `variants` | array | Variant definitions (e.g., Size, Color) |
| `variants[].id` | string | Variant group ID |
| `variants[].name` | string | Variant group name (e.g., "Size") |
| `variants[].options` | array | Options within this variant |
| `variants[].options[].id` | string | Option ID |
| `variants[].options[].name` | string | Option name (e.g., "XL") |
| `medias` | array | Product media gallery |
| `medias[].id` | string | Media ID |
| `medias[].title` | string | Media file title |
| `medias[].url` | string | Media URL |
| `medias[].type` | string | `image` or `video` |
| `medias[].isFeatured` | boolean | Whether this is the featured media |
| `medias[].priceIds` | array | Price IDs this media is assigned to |
| `collectionIds` | array\<string\> | Collection IDs this product belongs to |
| `isTaxesEnabled` | boolean | Whether taxes are enabled for this product |
| `taxes` | array\<string\> | Tax IDs attached to this product |
| `automaticTaxCategoryId` | string | Tax category ID for automatic tax calculation |
| `taxInclusive` | boolean | Whether prices include tax |
| `isLabelEnabled` | boolean | Whether product label badge is enabled |
| `label.title` | string | Label text (e.g., "Featured", "Sale") |
| `label.startDate` | string | Label display start (ISO 8601) |
| `label.endDate` | string | Label display end (ISO 8601) |
| `slug` | string | URL slug for product navigation |
| `seo.title` | string | SEO meta title |
| `seo.description` | string | SEO meta description |
| `createdAt` | string | ISO 8601 creation timestamp |
| `updatedAt` | string | ISO 8601 last update timestamp |

### Price Object

```json
{
  "_id": "00000000000000order00001",
  "product": "00000000000000product0001",
  "locationId": "EXAMPLELocation00000",
  "userId": "user_abc123",
  "name": "Standard - Medium",
  "type": "one_time",
  "currency": "USD",
  "amount": 29.99,
  "compareAtPrice": 39.99,
  "recurring": {
    "interval": "month",
    "intervalCount": 1
  },
  "trialPeriod": 0,
  "totalCycles": 12,
  "setupFee": 0,
  "variantOptionIds": ["opt_m"],
  "membershipOffers": [
    {
      "label": "Gold Membership",
      "value": "gold_offer",
      "_id": "offer_001"
    }
  ],
  "trackInventory": true,
  "availableQuantity": 50,
  "allowOutOfStockPurchases": false,
  "sku": "TSHIRT-M-001",
  "shippingOptions": {
    "weight": { "value": 200, "unit": "g" },
    "dimensions": { "height": 2, "width": 30, "length": 40, "unit": "cm" }
  },
  "isDigitalProduct": false,
  "digitalDelivery": [],
  "meta": {
    "source": "stripe",
    "sourceId": "ext_123",
    "stripePriceId": "price_abc",
    "internalSource": "funnel"
  },
  "createdAt": "2024-01-23T11:00:00.000Z",
  "updatedAt": "2024-01-23T11:00:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | MongoDB ObjectId — unique price identifier |
| `product` | string | Parent product ID |
| `locationId` | string | Location ID |
| `userId` | string | Creator user ID |
| `name` | string | Price name (e.g., "Standard Plan", "XL Blue") |
| `type` | string | `one_time` or `recurring` |
| `currency` | string | ISO currency code (e.g., "USD") |
| `amount` | number | Price amount (min: 0) |
| `compareAtPrice` | number | Strike-through / original price for display |
| `recurring.interval` | string | `day`, `week`, `month`, or `year` |
| `recurring.intervalCount` | number | Number of intervals between billings |
| `trialPeriod` | number | Trial period in days |
| `totalCycles` | number | Total billing cycles (min: 1) |
| `setupFee` | number | One-time setup fee |
| `variantOptionIds` | array\<string\> | Variant option IDs this price maps to |
| `membershipOffers` | array | Membership offer associations |
| `membershipOffers[].label` | string | Offer label |
| `membershipOffers[].value` | string | Offer value |
| `membershipOffers[]._id` | string | Offer ID |
| `trackInventory` | boolean | Whether to track stock for this variant |
| `availableQuantity` | number | Stock quantity available |
| `allowOutOfStockPurchases` | boolean | Continue selling when out of stock |
| `sku` | string | Stock Keeping Unit identifier |
| `shippingOptions.weight.value` | number | Product weight |
| `shippingOptions.weight.unit` | string | `kg`, `lb`, `g`, or `oz` |
| `shippingOptions.dimensions.height` | number | Product height |
| `shippingOptions.dimensions.width` | number | Product width |
| `shippingOptions.dimensions.length` | number | Product length |
| `shippingOptions.dimensions.unit` | string | `cm`, `in`, or `m` |
| `isDigitalProduct` | boolean | Whether this is a digital product |
| `digitalDelivery` | array\<string\> | Digital delivery options |
| `meta.source` | string | `stripe`, `woocommerce`, or `shopify` |
| `meta.sourceId` | string | External source ID |
| `meta.stripePriceId` | string | Stripe price ID |
| `meta.internalSource` | string | `agency_plan`, `funnel`, `membership`, `communities`, `gokollab`, `calendar` |
| `createdAt` | string | ISO 8601 creation timestamp |
| `updatedAt` | string | ISO 8601 last update timestamp |

### Collection Object

```json
{
  "_id": "0000000000000invoice0001",
  "altId": "EXAMPLELocation00000",
  "name": "Summer Collection",
  "slug": "summer-collection",
  "image": "https://storage.googleapis.com/example/summer-banner.png",
  "seo": {
    "title": "Summer Collection - Hot Deals",
    "description": "Browse our hottest summer products and exclusive deals."
  },
  "createdAt": "2024-02-15T08:00:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | MongoDB ObjectId |
| `altId` | string | Location ID |
| `name` | string | Collection name |
| `slug` | string | URL slug (no special characters or spaces) |
| `image` | string | Collection thumbnail URL |
| `seo.title` | string | SEO title |
| `seo.description` | string | SEO description |
| `createdAt` | string | ISO 8601 timestamp |

### Review Object

```json
{
  "reviewId": "rev_abc123",
  "productId": "00000000000000product0001",
  "storeId": "store_xyz789",
  "status": "approved",
  "rating": 5,
  "headline": "Best shirt I ever bought",
  "detail": "The quality is amazing. Fits perfectly and the material is very comfortable.",
  "reply": [
    {
      "headline": "Thank you!",
      "comment": "We appreciate your feedback!",
      "user": {
        "name": "Store Admin",
        "email": "admin@mybrand.com",
        "phone": "+15551234567",
        "isCustomer": false
      }
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `reviewId` | string | Review ID |
| `productId` | string | Product ID being reviewed |
| `storeId` | string | Store ID |
| `status` | string | Review status (e.g., "approved", "pending") |
| `rating` | number | Star rating |
| `headline` | string | Review headline |
| `detail` | string | Detailed review text |
| `reply` | array | Admin replies to the review |
| `reply[].headline` | string | Reply headline |
| `reply[].comment` | string | Reply text |
| `reply[].user.name` | string | Replier name |
| `reply[].user.email` | string | Replier email |
| `reply[].user.phone` | string | Replier phone |
| `reply[].user.isCustomer` | boolean | Whether replier is a customer vs admin |

### Inventory Record Object

```json
{
  "_id": "00000000000000order00001",
  "name": "Standard - Medium",
  "availableQuantity": 50,
  "sku": "TSHIRT-M-001",
  "allowOutOfStockPurchases": false,
  "product": "00000000000000product0001",
  "productName": "Premium T-Shirt",
  "image": "https://storage.googleapis.com/example/tshirt.png",
  "updatedAt": "2024-01-23T11:00:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Price/variant ID |
| `name` | string | Variant name |
| `availableQuantity` | number | Current stock level |
| `sku` | string | SKU code |
| `allowOutOfStockPurchases` | boolean | Allow purchasing when out of stock |
| `product` | string | Parent product ID |
| `productName` | string | Parent product name |
| `image` | string | Product image URL |
| `updatedAt` | string | Last update timestamp |

---

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 400 | Bad Request — invalid parameters or body |
| 401 | Unauthorized — invalid/expired token or insufficient scope |
| 422 | Unprocessable Entity — validation failure |

---

## General Notes

- **Access types:** Most endpoints accept both Location-Access (Sub-Account token) and Agency-Access tokens. Exceptions: Bulk Update Products, Collections, Reviews, and Store endpoints are Location-Access only.
- **Bulk Edit Products and Prices** has no explicit security/scope defined in the OpenAPI spec. It likely requires `products.write` but this is unconfirmed.
- **Update Store Display Priorities** has no explicit scopes listed in the OpenAPI spec.
- **Products and Prices relationship:** Products have a one-to-many relationship with prices. Products contain variant definitions; prices reference variant options via `variantOptionIds`.
- **Products and Collections:** Many-to-many relationship via the `collectionIds` array on products.
- **Stripe integration:** The `meta.stripePriceId` field on prices links to Stripe. Use `expand=stripe` on List Products to hydrate Stripe data.
- **Product slugs:** The `productId` path parameter on Get, Update, and Delete Product endpoints accepts either a product ID or a product slug.
- **Unusual `total` structure:** The List Products endpoint returns `total` as an array of objects (`[{"total": N}]`), which is an unusual response structure to watch for.
- **Collection `altType` casing:** Collection endpoints use `altType: "LOCATION"` (uppercase) for Create/Update, but `altType: "location"` (lowercase) for List/Delete. Be careful with casing.
- **Bulk Update vs Bulk Edit:** Bulk Update Products operates on product-level attributes across multiple products (price adjustments, availability, etc.), while Bulk Edit Products and Prices allows inline editing of individual product and price fields in a single request.

---

> **Agent Note:** Documentation created from OpenAPI spec research. All endpoints are 🔬 unverified until tested against a live GHL API.
