# Products API — Products (Core)

Core product CRUD and bulk operations. See [schemas.md](./schemas.md) for object schemas, scopes, auth, and rate limits.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

| Endpoint | Method | Scope |
|----------|--------|-------|
| [Create Product](#create-product) | POST | products.write |
| [List Products](#list-products) | GET | products.readonly |
| [Get Product by ID](#get-product-by-id) | GET | products.readonly |
| [Update Product by ID](#update-product-by-id) | PUT | products.write |
| [Delete Product by ID](#delete-product-by-id) | DELETE | products.write |
| [Bulk Update Products](#bulk-update-products) | POST | products.write |
| [Bulk Edit Products and Prices](#bulk-edit-products-and-prices) | POST | *(not specified)* |

---

## Create Product

Create a new product in the catalog.

**Method:** `POST`
**URL:** `/products/`
**Scope:** `products.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Product name |
| `locationId` | string | Yes | Location (sub-account) ID |
| `productType` | string | Yes | `DIGITAL`, `PHYSICAL`, `SERVICE`, or `PHYSICAL/DIGITAL` |
| `description` | string | No | Product description |
| `image` | string | No | Primary image URL |
| `statementDescriptor` | string | No | Bank statement descriptor |
| `availableInStore` | boolean | No | Show in store |
| `medias` | array | No | Media gallery (see Product Object) |
| `variants` | array | No | Variant definitions (see Product Object) |
| `collectionIds` | array\<string\> | No | Collection IDs to assign |
| `isTaxesEnabled` | boolean | No | Enable taxes (default: `false`) |
| `taxes` | array\<string\> | No | Tax IDs (requires `isTaxesEnabled: true`) |
| `automaticTaxCategoryId` | string | No | Auto tax category ID |
| `taxInclusive` | boolean | No | Tax-inclusive pricing (default: `false`) |
| `isLabelEnabled` | boolean | No | Enable product label (default: `false`) |
| `label` | object | No | Label config (requires `isLabelEnabled: true`) |
| `label.title` | string | Yes* | Label text (*required if label is provided) |
| `label.startDate` | string | No | ISO 8601 start date |
| `label.endDate` | string | No | ISO 8601 end date |
| `slug` | string | No | URL slug |
| `seo` | object | No | SEO metadata |
| `seo.title` | string | No | SEO title |
| `seo.description` | string | No | SEO description |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/products/' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Premium T-Shirt",
    "locationId": "EXAMPLELocation00000",
    "productType": "PHYSICAL",
    "description": "High-quality cotton t-shirt available in multiple sizes.",
    "image": "https://storage.googleapis.com/example/tshirt.png",
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
    "collectionIds": ["0000000000000invoice0001"],
    "slug": "premium-t-shirt",
    "seo": {
      "title": "Premium T-Shirt - Best Quality",
      "description": "Shop our premium cotton t-shirt in S, M, L, XL."
    }
  }'
```

### Response Example

```json
{
  "_id": "00000000000000product0001",
  "name": "Premium T-Shirt",
  "description": "High-quality cotton t-shirt available in multiple sizes.",
  "productType": "PHYSICAL",
  "locationId": "EXAMPLELocation00000",
  "image": "https://storage.googleapis.com/example/tshirt.png",
  "availableInStore": true,
  "statementDescriptor": null,
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
  "collectionIds": ["0000000000000invoice0001"],
  "isTaxesEnabled": false,
  "taxes": [],
  "slug": "premium-t-shirt",
  "createdAt": "2024-01-23T10:30:00.000Z",
  "updatedAt": "2024-01-23T10:30:00.000Z"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## List Products

Retrieve a paginated list of products.

**Method:** `GET`
**URL:** `/products/`
**Scope:** `products.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location (sub-account) ID |
| `limit` | number | No | Max items per page (default: 0 = all) |
| `offset` | number | No | Pagination offset (default: 0) |
| `search` | string | No | Search by product name |
| `collectionIds` | string | No | Filter by collection IDs (comma-separated) |
| `collectionSlug` | string | No | Filter by collection slug |
| `expand` | array | No | Expand related entities: `tax`, `stripe` |
| `productIds` | array | No | Filter to specific product IDs |
| `storeId` | string | No | Filter/project by store |
| `includedInStore` | boolean | No | Filter by store inclusion status |
| `availableInStore` | boolean | No | Filter by online store availability |
| `sortOrder` | string | No | `asc` or `desc` (by date) |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/?locationId=EXAMPLELocation00000&limit=10&offset=0&search=shirt' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "products": [
    {
      "_id": "00000000000000product0001",
      "name": "Premium T-Shirt",
      "description": "High-quality cotton t-shirt.",
      "productType": "PHYSICAL",
      "locationId": "EXAMPLELocation00000",
      "image": "https://storage.googleapis.com/example/tshirt.png",
      "availableInStore": true,
      "variants": [],
      "collectionIds": ["0000000000000invoice0001"],
      "isTaxesEnabled": false,
      "taxes": [],
      "slug": "premium-t-shirt",
      "createdAt": "2024-01-23T10:30:00.000Z",
      "updatedAt": "2024-01-23T10:30:00.000Z"
    }
  ],
  "total": [{ "total": 1 }]
}
```

### Important Notes

- The `total` field is an array of objects with a `total` property (unusual structure).
- `expand=tax` includes full tax objects instead of just IDs.
- `expand=stripe` includes Stripe metadata on prices.

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Get Product by ID

Retrieve a single product by ID or slug.

**Method:** `GET`
**URL:** `/products/{productId}`
**Scope:** `products.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `productId` | string | Yes | Product ID **or** product slug |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location ID |
| `sendWishlistStatus` | boolean | No | Include wishlist status in response |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/00000000000000product0001?locationId=EXAMPLELocation00000' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "00000000000000product0001",
  "name": "Premium T-Shirt",
  "description": "High-quality cotton t-shirt available in multiple sizes.",
  "productType": "PHYSICAL",
  "locationId": "EXAMPLELocation00000",
  "image": "https://storage.googleapis.com/example/tshirt.png",
  "availableInStore": true,
  "statementDescriptor": null,
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
  "medias": [],
  "collectionIds": ["0000000000000invoice0001"],
  "isTaxesEnabled": false,
  "taxes": [],
  "slug": "premium-t-shirt",
  "seo": {
    "title": "Premium T-Shirt - Best Quality",
    "description": "Shop our premium cotton t-shirt in S, M, L, XL."
  },
  "createdAt": "2024-01-23T10:30:00.000Z",
  "updatedAt": "2024-01-23T10:30:00.000Z"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Update Product by ID

Update an existing product.

**Method:** `PUT`
**URL:** `/products/{productId}`
**Scope:** `products.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `productId` | string | Yes | Product ID or slug |

### Request Body

All fields from [Create Product](#create-product) are accepted (all optional), plus:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | No | Product name |
| `locationId` | string | No | Location ID |
| `productType` | string | No | Product type |
| `description` | string | No | Product description |
| `image` | string | No | Primary image URL |
| `availableInStore` | boolean | No | Show in store |
| `variants` | array | No | Variant definitions |
| `medias` | array | No | Media gallery |
| `collectionIds` | array\<string\> | No | Collection IDs |
| `prices` | array\<string\> | No | Price IDs to associate (for reordering) |
| `slug` | string | No | URL slug |
| `seo` | object | No | SEO metadata |

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/products/00000000000000product0001' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Premium Cotton T-Shirt",
    "description": "Updated description — now available in 5 sizes.",
    "availableInStore": true,
    "slug": "premium-cotton-t-shirt"
  }'
```

### Response Example

```json
{
  "_id": "00000000000000product0001",
  "name": "Premium Cotton T-Shirt",
  "description": "Updated description — now available in 5 sizes.",
  "productType": "PHYSICAL",
  "locationId": "EXAMPLELocation00000",
  "image": "https://storage.googleapis.com/example/tshirt.png",
  "availableInStore": true,
  "slug": "premium-cotton-t-shirt",
  "createdAt": "2024-01-23T10:30:00.000Z",
  "updatedAt": "2024-02-15T14:20:00.000Z"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Delete Product by ID

Delete a product permanently.

**Method:** `DELETE`
**URL:** `/products/{productId}`
**Scope:** `products.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `productId` | string | Yes | Product ID or slug |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location ID |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/products/00000000000000product0001?locationId=EXAMPLELocation00000' \
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

## Bulk Update Products

Perform bulk operations on multiple products (price adjustments, availability changes, collection assignments, deletions, currency changes).

**Method:** `POST`
**URL:** `/products/bulk-update`
**Scope:** `products.write`
**Auth:** Location-Access only

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | Always `location` |
| `type` | string | Yes | Operation type (see below) |
| `productIds` | array\<string\> | Yes | Product IDs to operate on |
| `filters` | object | No | Additional filters |
| `filters.collectionIds` | array\<string\> | No | Filter by collections |
| `filters.productType` | string | No | Filter by product type |
| `filters.availableInStore` | boolean | No | Filter by availability |
| `filters.search` | string | No | Filter by search term |
| `price` | object | No | Price update config (for `bulk-update-price`) |
| `price.type` | string | Yes* | `INCREASE_BY_AMOUNT`, `REDUCE_BY_AMOUNT`, `SET_NEW_PRICE`, `INCREASE_BY_PERCENTAGE`, `REDUCE_BY_PERCENTAGE` |
| `price.value` | number | Yes* | Amount or percentage |
| `price.roundToWhole` | boolean | No | Round to nearest whole number |
| `compareAtPrice` | object | No | Compare-at price update (same structure as `price`) |
| `availability` | boolean | No | New availability status (for `bulk-update-availability`) |
| `collectionIds` | array\<string\> | No | Collection IDs (for `bulk-update-product-collection`) |
| `currency` | string | No | Currency code (for `bulk-update-currency`) |

**Bulk operation types:**
- `bulk-update-price` — Adjust prices across products
- `bulk-update-availability` — Toggle availability
- `bulk-update-product-collection` — Assign collections
- `bulk-delete-products` — Delete multiple products
- `bulk-update-currency` — Change currency

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/products/bulk-update' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "type": "bulk-update-price",
    "productIds": ["00000000000000product0001", "00000000000000product0002"],
    "price": {
      "type": "INCREASE_BY_PERCENTAGE",
      "value": 10,
      "roundToWhole": true
    }
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Products updated successfully"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Bulk Edit Products and Prices

Inline edit multiple products and their prices in a single request.

**Method:** `POST`
**URL:** `/products/bulk-update/edit`
**Scope:** *Not explicitly specified in OpenAPI spec*

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | Always `location` |
| `products` | array | Yes | Products to update |
| `products[]._id` | string | Yes | Product ID |
| `products[].name` | string | No | New name |
| `products[].description` | string | No | New description |
| `products[].image` | string | No | New image URL |
| `products[].availableInStore` | boolean | No | Store availability |
| `products[].prices` | array | No | Price updates |
| `products[].prices[]._id` | string | Yes | Price ID |
| `products[].prices[].name` | string | No | Price name |
| `products[].prices[].amount` | number | No | Price amount |
| `products[].prices[].currency` | string | No | Currency code |
| `products[].prices[].compareAtPrice` | number | No | Compare-at price |
| `products[].prices[].availableQuantity` | number | No | Stock quantity |
| `products[].prices[].trackInventory` | boolean | No | Track inventory |
| `products[].prices[].allowOutOfStockPurchases` | boolean | No | Allow OOS purchases |
| `products[].prices[].sku` | string | No | SKU |
| `products[].prices[].trialPeriod` | number | No | Trial days |
| `products[].prices[].totalCycles` | number | No | Total billing cycles |
| `products[].prices[].setupFee` | number | No | Setup fee |
| `products[].prices[].shippingOptions` | object | No | Shipping weight/dimensions |
| `products[].prices[].recurring` | object | No | Recurring billing config |
| `products[].collectionIds` | array\<string\> | No | Collection assignments |
| `products[].slug` | string | No | URL slug |
| `products[].seo` | object | No | SEO metadata |
| `products[].taxes` | array | No | Tax assignments |
| `products[].medias` | array | No | Media gallery |
| `products[].label` | object | No | Product label |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/products/bulk-update/edit' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "products": [
      {
        "_id": "00000000000000product0001",
        "name": "Premium T-Shirt v2",
        "prices": [
          {
            "_id": "00000000000000order00001",
            "amount": 34.99,
            "compareAtPrice": 44.99
          }
        ]
      },
      {
        "_id": "00000000000000product0002",
        "availableInStore": false
      }
    ]
  }'
```

### Response Example

```json
{
  "message": "Products and prices updated successfully",
  "status": true,
  "updatedCount": 2
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.
