# Products API — Collections

Product collection/category organization. See [schemas.md](./schemas.md) for object schemas, scopes, auth, and rate limits.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

| Endpoint | Method | Scope |
|----------|--------|-------|
| [List Collections](#list-collections) | GET | products/collection.readonly |
| [Create Collection](#create-collection) | POST | products/collection.write |
| [Get Collection](#get-collection) | GET | products/collection.readonly |
| [Update Collection](#update-collection) | PUT | products/collection.write |
| [Delete Collection](#delete-collection) | DELETE | products/collection.write |

> **Casing gotcha:** Collection endpoints use `altType: "LOCATION"` (uppercase) for Create/Update, but `altType: "location"` (lowercase) for List/Delete.

---

## List Collections

List all product collections for a location.

**Method:** `GET`
**URL:** `/products/collections`
**Scope:** `products/collection.readonly`
**Auth:** Location-Access only

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |
| `limit` | number | No | Pagination limit (default: 10) |
| `offset` | number | No | Pagination offset (default: 0) |
| `collectionIds` | string | No | Comma-separated collection IDs |
| `name` | string | No | Search by collection name |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/collections?altId=EXAMPLELocation00000&altType=location&limit=10&offset=0' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "collections": [
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
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Create Collection

Create a new product collection/category.

**Method:** `POST`
**URL:** `/products/collections`
**Scope:** `products/collection.write`
**Auth:** Location-Access only

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `LOCATION` |
| `name` | string | Yes | Collection name |
| `slug` | string | Yes | URL slug (no special characters or spaces) |
| `collectionId` | string | No | Custom MongoDB ID |
| `image` | string | No | Thumbnail URL |
| `seo.title` | string | No | SEO title |
| `seo.description` | string | No | SEO description |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/products/collections' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "LOCATION",
    "name": "Winter Sale",
    "slug": "winter-sale",
    "image": "https://storage.googleapis.com/example/winter-banner.png",
    "seo": {
      "title": "Winter Sale - Up to 50% Off",
      "description": "Shop our winter sale for the best deals on seasonal products."
    }
  }'
```

### Response Example

```json
{
  "_id": "0000000000000invoice0002",
  "altId": "EXAMPLELocation00000",
  "name": "Winter Sale",
  "slug": "winter-sale",
  "image": "https://storage.googleapis.com/example/winter-banner.png",
  "seo": {
    "title": "Winter Sale - Up to 50% Off",
    "description": "Shop our winter sale for the best deals on seasonal products."
  },
  "createdAt": "2024-03-18T09:00:00.000Z"
}
```

### Important Notes

- The `altType` value must be uppercase `LOCATION` (different from other endpoints that use lowercase `location`).

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Get Collection

Retrieve a single collection by ID.

**Method:** `GET`
**URL:** `/products/collections/{collectionId}`
**Scope:** `products/collection.readonly`
**Auth:** Location-Access only

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `collectionId` | string | Yes | Collection ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/products/collections/0000000000000invoice0001?altId=EXAMPLELocation00000' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28'
```

### Response Example

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

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Update Collection

Update an existing collection.

**Method:** `PUT`
**URL:** `/products/collections/{collectionId}`
**Scope:** `products/collection.write`
**Auth:** Location-Access only

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `collectionId` | string | Yes | Collection ID |

### Request Body

Same fields as [Create Collection](#create-collection), except `altId` and `altType` are required and all other fields are optional.

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/products/collections/0000000000000invoice0001' \
  -H 'Authorization: Bearer {access_token}' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "EXAMPLELocation00000",
    "altType": "LOCATION",
    "name": "Summer Collection 2024",
    "seo": {
      "title": "Summer Collection 2024 - New Arrivals",
      "description": "Discover our refreshed summer lineup with new products."
    }
  }'
```

### Response Example

```json
{
  "_id": "0000000000000invoice0001",
  "altId": "EXAMPLELocation00000",
  "name": "Summer Collection 2024",
  "slug": "summer-collection",
  "image": "https://storage.googleapis.com/example/summer-banner.png",
  "seo": {
    "title": "Summer Collection 2024 - New Arrivals",
    "description": "Discover our refreshed summer lineup with new products."
  },
  "createdAt": "2024-02-15T08:00:00.000Z"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Delete Collection

Delete a product collection.

**Method:** `DELETE`
**URL:** `/products/collections/{collectionId}`
**Scope:** `products/collection.write`
**Auth:** Location-Access only

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `collectionId` | string | Yes | Collection ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `location` |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/products/collections/0000000000000invoice0001?altId=EXAMPLELocation00000&altType=location' \
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
