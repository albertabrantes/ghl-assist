# Products API — Reviews & Store

Product review moderation and store inclusion/display management. See [schemas.md](./schemas.md) for object schemas, scopes, auth, and rate limits.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

| Endpoint | Method | Scope |
|----------|--------|-------|
| [List Reviews](#list-reviews) | GET | products.readonly |
| [Bulk Update Reviews](#bulk-update-reviews) | POST | products.write |
| [Get Review Count](#get-review-count) | GET | products.readonly |
| [Update Review](#update-review) | PUT | products.write |
| [Delete Review](#delete-review) | DELETE | products.write |
| [Include/Exclude Product in Store](#includeexclude-product-in-store) | POST | products.write |
| [Update Store Display Priorities](#update-store-display-priorities) | POST | *(none listed)* |
| [Get Store Stats](#get-store-stats) | GET | products.readonly |

All Reviews and Store endpoints are **Location-Access only**.

---

## List Reviews

List product reviews with filtering and sorting.

**Method:** `GET`
**URL:** `/products/reviews`
**Scope:** `products.readonly`
**Auth:** Location-Access only

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `limit` | number | No | Pagination limit |
| `offset` | number | No | Pagination offset |
| `sortField` | string | No | `createdAt` or `rating` |
| `sortOrder` | string | No | `asc` or `desc` |
| `rating` | number | No | Filter by rating |
| `startDate` | string | No | Filter start date (ISO 8601) |
| `endDate` | string | No | Filter end date (ISO 8601) |
| `productId` | string | No | Comma-separated product IDs |
| `storeId` | string | No | Comma-separated store IDs |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/reviews?altId=EXAMPLELocation00000&altType=location&limit=10&offset=0&sortField=createdAt&sortOrder=desc' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "reviews": [
    {
      "reviewId": "rev_abc123",
      "productId": "00000000000000product0001",
      "storeId": "store_xyz789",
      "status": "approved",
      "rating": 5,
      "headline": "Best shirt I ever bought",
      "detail": "The quality is amazing. Fits perfectly and the material is very comfortable.",
      "reply": [],
      "createdAt": "2024-03-10T14:00:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Bulk Update Reviews

Bulk update the status of multiple reviews at once.

**Method:** `POST`
**URL:** `/products/reviews/bulk-update`
**Scope:** `products.write`
**Auth:** Location-Access only

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `reviews` | array | Yes | Reviews to update |
| `reviews[].reviewId` | string | Yes | Review ID |
| `reviews[].productId` | string | Yes | Product ID |
| `reviews[].storeId` | string | Yes | Store ID |
| `status` | string | Yes | New status (e.g., `"approved"`, `"pending"`) |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/products/reviews/bulk-update' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "reviews": [
      { "reviewId": "rev_abc123", "productId": "00000000000000product0001", "storeId": "store_xyz789" },
      { "reviewId": "rev_def456", "productId": "00000000000000product0002", "storeId": "store_xyz789" }
    ],
    "status": "approved"
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Reviews updated successfully"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Get Review Count

Get review counts grouped by status.

**Method:** `GET`
**URL:** `/products/reviews/count`
**Scope:** `products.readonly`
**Auth:** Location-Access only

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `rating` | number | No | Filter by rating |
| `startDate` | string | No | Filter start date (ISO 8601) |
| `endDate` | string | No | Filter end date (ISO 8601) |
| `productId` | string | No | Comma-separated product IDs |
| `storeId` | string | No | Comma-separated store IDs |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/reviews/count?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "approved": 42,
  "pending": 5,
  "rejected": 2,
  "total": 49
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Update Review

Update a single review (change status, add reply, edit content).

**Method:** `PUT`
**URL:** `/products/reviews/{reviewId}`
**Scope:** `products.write`
**Auth:** Location-Access only

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `reviewId` | string | Yes | Review ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `productId` | string | Yes | Product ID |
| `status` | string | Yes | Review status (e.g., `"approved"`) |
| `reply` | array | No | Reply thread |
| `reply[].headline` | string | Yes | Reply headline |
| `reply[].comment` | string | Yes | Reply body |
| `reply[].user.name` | string | Yes | Replier name |
| `reply[].user.email` | string | Yes | Replier email |
| `reply[].user.phone` | string | No | Replier phone |
| `reply[].user.isCustomer` | boolean | No | Is customer? |
| `rating` | number | No | Updated rating |
| `headline` | string | No | Updated headline |
| `detail` | string | No | Updated detail text |

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/products/reviews/rev_abc123' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "productId": "00000000000000product0001",
    "status": "approved",
    "reply": [
      {
        "headline": "Thank you for your review!",
        "comment": "We are glad you enjoy the product. Thanks for the 5 stars!",
        "user": {
          "name": "Store Admin",
          "email": "admin@mybrand.com",
          "isCustomer": false
        }
      }
    ]
  }'
```

### Response Example

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
      "headline": "Thank you for your review!",
      "comment": "We are glad you enjoy the product. Thanks for the 5 stars!",
      "user": {
        "name": "Store Admin",
        "email": "admin@mybrand.com",
        "isCustomer": false
      }
    }
  ]
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Delete Review

Delete a product review.

**Method:** `DELETE`
**URL:** `/products/reviews/{reviewId}`
**Scope:** `products.write`
**Auth:** Location-Access only

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `reviewId` | string | Yes | Review ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `productId` | string | Yes | Product ID |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/products/reviews/rev_abc123?altId=EXAMPLELocation00000&altType=location&productId=00000000000000product0001' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "status": true
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Include/Exclude Product in Store

Add or remove products from a store's catalog.

**Method:** `POST`
**URL:** `/products/store/{storeId}`
**Scope:** `products.write`
**Auth:** Location-Access only

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `storeId` | string | Yes | Store ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `action` | string | Yes | `include` or `exclude` |
| `productIds` | array\<string\> | Yes | Product IDs to include/exclude |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/products/store/store_xyz789' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "action": "include",
    "productIds": ["00000000000000product0001", "00000000000000product0002"]
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Products included in store successfully"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Update Store Display Priorities

Set the display order/priority of products within a store.

**Method:** `POST`
**URL:** `/products/store/{storeId}/priority`
**Scope:** *(none specified in OpenAPI spec)*
**Auth:** Location-Access only

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `storeId` | string | Yes | Store ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `products` | array | Yes | Array of products with priority info |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/products/store/store_xyz789/priority' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "products": [
      { "productId": "00000000000000product0001", "priority": 1 },
      { "productId": "00000000000000product0002", "priority": 2 },
      { "productId": "00000000000000product0003", "priority": 3 }
    ]
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Store display priorities updated successfully"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Get Store Stats

Get product statistics for a store (total products, included/excluded counts).

**Method:** `GET`
**URL:** `/products/store/{storeId}/stats`
**Scope:** `products.readonly`
**Auth:** Location-Access only

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `storeId` | string | Yes | Store ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `search` | string | No | Search products |
| `collectionIds` | string | No | Filter by collection IDs |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/store/store_xyz789/stats?altId=EXAMPLELocation00000&altType=location' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "totalProducts": 42,
  "includedInStore": 35,
  "excludedFromStore": 7
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.
