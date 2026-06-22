# Products API — Prices & Inventory

Price/variant management and inventory tracking. See [schemas.md](./schemas.md) for object schemas, scopes, auth, and rate limits.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

| Endpoint | Method | Scope |
|----------|--------|-------|
| [Create Price](#create-price) | POST | products/prices.write |
| [List Prices](#list-prices) | GET | products/prices.readonly |
| [Get Price by ID](#get-price-by-id) | GET | products/prices.readonly |
| [Update Price by ID](#update-price-by-id) | PUT | products/prices.write |
| [Delete Price by ID](#delete-price-by-id) | DELETE | products/prices.write |
| [List Inventory](#list-inventory) | GET | products/prices.readonly |
| [Update Inventory](#update-inventory) | POST | products/prices.write |

Products have a one-to-many relationship with prices. Prices reference variant options via `variantOptionIds`.

---

## Create Price

Create a new price/variant for an existing product.

**Method:** `POST`
**URL:** `/products/{productId}/price`
**Scope:** `products/prices.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `productId` | string | Yes | Product ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Price name |
| `type` | string | Yes | `one_time` or `recurring` |
| `currency` | string | Yes | ISO currency code |
| `amount` | number | Yes | Price amount (min: 0) |
| `locationId` | string | Yes | Location ID |
| `recurring` | object | No | Required if `type` is `recurring` |
| `recurring.interval` | string | Yes* | `day`, `week`, `month`, or `year` (* required if recurring) |
| `recurring.intervalCount` | number | Yes* | Intervals between billing (* required if recurring) |
| `description` | string | No | Price description |
| `membershipOffers` | array | No | Membership offer associations |
| `trialPeriod` | number | No | Trial period in days |
| `totalCycles` | number | No | Total billing cycles (min: 1) |
| `setupFee` | number | No | One-time setup fee |
| `variantOptionIds` | array\<string\> | No | Map to variant options |
| `compareAtPrice` | number | No | Strikethrough price |
| `userId` | string | No | Creator user ID |
| `meta` | object | No | External source metadata |
| `meta.source` | string | No | `stripe`, `woocommerce`, `shopify` |
| `meta.stripePriceId` | string | No | Stripe price ID |
| `meta.internalSource` | string | No | `agency_plan`, `funnel`, `membership`, `communities`, `gokollab`, `calendar` |
| `trackInventory` | boolean | No | Enable inventory tracking |
| `availableQuantity` | number | No | Initial stock quantity |
| `allowOutOfStockPurchases` | boolean | No | Allow OOS purchases |
| `sku` | string | No | SKU code |
| `shippingOptions` | object | No | Shipping weight & dimensions |
| `isDigitalProduct` | boolean | No | Digital product flag |
| `digitalDelivery` | array\<string\> | No | Digital delivery options |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/products/00000000000000product0001/price' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Standard - Medium",
    "type": "one_time",
    "currency": "USD",
    "amount": 29.99,
    "locationId": "EXAMPLELocation00000",
    "compareAtPrice": 39.99,
    "variantOptionIds": ["opt_m"],
    "trackInventory": true,
    "availableQuantity": 50,
    "sku": "TSHIRT-M-001",
    "shippingOptions": {
      "weight": { "value": 200, "unit": "g" },
      "dimensions": { "height": 2, "width": 30, "length": 40, "unit": "cm" }
    }
  }'
```

### Response Example

```json
{
  "_id": "00000000000000order00001",
  "name": "Standard - Medium",
  "type": "one_time",
  "currency": "USD",
  "amount": 29.99,
  "compareAtPrice": 39.99,
  "product": "00000000000000product0001",
  "locationId": "EXAMPLELocation00000",
  "variantOptionIds": ["opt_m"],
  "membershipOffers": [],
  "trackInventory": true,
  "availableQuantity": 50,
  "allowOutOfStockPurchases": false,
  "createdAt": "2024-01-23T11:00:00.000Z",
  "updatedAt": "2024-01-23T11:00:00.000Z"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## List Prices

List all prices/variants for a product.

**Method:** `GET`
**URL:** `/products/{productId}/price`
**Scope:** `products/prices.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `productId` | string | Yes | Product ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location ID |
| `limit` | number | No | Max items per page |
| `offset` | number | No | Pagination offset |
| `ids` | string | No | Comma-separated price IDs to filter |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/00000000000000product0001/price?locationId=EXAMPLELocation00000&limit=10&offset=0' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "prices": [
    {
      "_id": "00000000000000order00001",
      "name": "Standard - Medium",
      "type": "one_time",
      "currency": "USD",
      "amount": 29.99,
      "compareAtPrice": 39.99,
      "product": "00000000000000product0001",
      "locationId": "EXAMPLELocation00000",
      "variantOptionIds": ["opt_m"],
      "trackInventory": true,
      "availableQuantity": 50,
      "sku": "TSHIRT-M-001",
      "createdAt": "2024-01-23T11:00:00.000Z",
      "updatedAt": "2024-01-23T11:00:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Get Price by ID

Retrieve a single price/variant by ID.

**Method:** `GET`
**URL:** `/products/{productId}/price/{priceId}`
**Scope:** `products/prices.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `productId` | string | Yes | Product ID |
| `priceId` | string | Yes | Price ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location ID |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/00000000000000product0001/price/00000000000000order00001?locationId=EXAMPLELocation00000' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "00000000000000order00001",
  "name": "Standard - Medium",
  "type": "one_time",
  "currency": "USD",
  "amount": 29.99,
  "compareAtPrice": 39.99,
  "product": "00000000000000product0001",
  "locationId": "EXAMPLELocation00000",
  "variantOptionIds": ["opt_m"],
  "membershipOffers": [],
  "trackInventory": true,
  "availableQuantity": 50,
  "allowOutOfStockPurchases": false,
  "sku": "TSHIRT-M-001",
  "shippingOptions": {
    "weight": { "value": 200, "unit": "g" },
    "dimensions": { "height": 2, "width": 30, "length": 40, "unit": "cm" }
  },
  "createdAt": "2024-01-23T11:00:00.000Z",
  "updatedAt": "2024-01-23T11:00:00.000Z"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Update Price by ID

Update an existing price/variant.

**Method:** `PUT`
**URL:** `/products/{productId}/price/{priceId}`
**Scope:** `products/prices.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `productId` | string | Yes | Product ID |
| `priceId` | string | Yes | Price ID |

### Request Body

Same fields as [Create Price](#create-price), all optional. Include only the fields you want to change.

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/products/00000000000000product0001/price/00000000000000order00001' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "amount": 34.99,
    "compareAtPrice": 49.99,
    "availableQuantity": 75,
    "locationId": "EXAMPLELocation00000"
  }'
```

### Response Example

```json
{
  "_id": "00000000000000order00001",
  "name": "Standard - Medium",
  "type": "one_time",
  "currency": "USD",
  "amount": 34.99,
  "compareAtPrice": 49.99,
  "product": "00000000000000product0001",
  "locationId": "EXAMPLELocation00000",
  "trackInventory": true,
  "availableQuantity": 75,
  "createdAt": "2024-01-23T11:00:00.000Z",
  "updatedAt": "2024-02-15T16:30:00.000Z"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Delete Price by ID

Delete a price/variant from a product.

**Method:** `DELETE`
**URL:** `/products/{productId}/price/{priceId}`
**Scope:** `products/prices.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `productId` | string | Yes | Product ID |
| `priceId` | string | Yes | Price ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location ID |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/products/00000000000000product0001/price/00000000000000order00001?locationId=EXAMPLELocation00000' \
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

## List Inventory

View inventory levels across all products/prices.

**Method:** `GET`
**URL:** `/products/inventory`
**Scope:** `products/prices.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | Always `location` |
| `limit` | number | No | Pagination limit |
| `offset` | number | No | Pagination offset |
| `search` | string | No | Search variant names |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/inventory?altId=EXAMPLELocation00000&altType=location&limit=20&offset=0' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "inventory": [
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
  ],
  "total": { "total": 1 }
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Update Inventory

Bulk update inventory quantities for one or more price/variant records.

**Method:** `POST`
**URL:** `/products/inventory`
**Scope:** `products/prices.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `items` | array | Yes | Inventory updates |
| `items[].priceId` | string | Yes | Price/variant ID |
| `items[].availableQuantity` | number | No | New stock quantity |
| `items[].allowOutOfStockPurchases` | boolean | No | Allow OOS purchases |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/products/inventory' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "location",
    "items": [
      { "priceId": "00000000000000order00001", "availableQuantity": 100 },
      { "priceId": "00000000000000order00002", "availableQuantity": 0, "allowOutOfStockPurchases": true }
    ]
  }'
```

### Response Example

```json
{
  "status": true,
  "message": "Inventory updated successfully"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.
