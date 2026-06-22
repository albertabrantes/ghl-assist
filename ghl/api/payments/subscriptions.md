# Payments API — Subscriptions & Coupons

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

This file covers recurring subscriptions (list/retrieve) and full CRUD for promotional discount coupons.

## Subscription Object

```json
{
  "_id": "sub_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "contactId": "contact_123",
  "status": "active",
  "entityId": "entity_123",
  "entitySourceType": "funnel",
  "paymentMode": "live",
  "startAt": "2026-01-15T00:00:00.000Z",
  "endAt": null,
  "createdAt": "2026-01-15T10:30:00.000Z",
  "updatedAt": "2026-01-15T10:35:00.000Z"
}
```

## Coupon Object

```json
{
  "_id": "coupon_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Summer Sale 20%",
  "code": "SUMMER20",
  "discountType": "percentage",
  "discountValue": 20,
  "duration": "once",
  "durationInMonths": null,
  "maxUses": 100,
  "startDate": "2026-06-01T00:00:00.000Z",
  "endDate": "2026-08-31T23:59:59.000Z",
  "productIds": ["prod_123", "prod_456"]
}
```

### Coupon Configuration Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Coupon display name |
| `code` | string | Promotional code (what customers enter) |
| `discountType` | string | `"percentage"` or `"fixed"` |
| `discountValue` | number | Discount amount (percentage value or fixed currency amount) |
| `duration` | string | `"forever"`, `"multiple_months"`, or `"once"` (for subscription coupons) |
| `durationInMonths` | number | Number of months (when duration is `"multiple_months"`) |
| `maxUses` | number | Maximum total uses allowed |
| `startDate` | string | When the coupon becomes active |
| `endDate` | string | When the coupon expires |
| `productIds` | array[string] | Product IDs the coupon applies to |

---

## List Subscriptions

Retrieve a paginated list of subscriptions with filtering.

**Method:** `GET`
**URL:** `/payments/subscriptions`
**Scope:** `payments/subscriptions.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `status` | string | No | Subscription status filter |
| `contactId` | string | No | Filter by contact ID |
| `entityId` | string | No | Filter by entity ID |
| `entitySourceType` | string | No | Filter by entity source type |
| `search` | string | No | Search term |
| `startAt` | string | No | Date range start |
| `endAt` | string | No | Date range end |
| `paymentMode` | string | No | Payment mode filter |
| `limit` | number | No | Page size |
| `offset` | number | No | Pagination offset |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/subscriptions?altId=loc_abc123&altType=location&status=active' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "subscriptions": [
    {
      "_id": "sub_abc123",
      "altId": "loc_abc123",
      "altType": "location",
      "contactId": "contact_123",
      "status": "active",
      "entityId": "entity_123",
      "entitySourceType": "funnel",
      "paymentMode": "live",
      "createdAt": "2026-01-15T10:30:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Get Subscription by ID

Retrieve a specific subscription by its ID.

**Method:** `GET`
**URL:** `/payments/subscriptions/{subscriptionId}`
**Scope:** `payments/subscriptions.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `subscriptionId` | string | Yes | Subscription ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/subscriptions/sub_abc123?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "sub_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "contactId": "contact_123",
  "status": "active",
  "entityId": "entity_123",
  "entitySourceType": "funnel",
  "paymentMode": "live",
  "startAt": "2026-01-15T00:00:00.000Z",
  "endAt": null,
  "createdAt": "2026-01-15T10:30:00.000Z",
  "updatedAt": "2026-01-15T10:30:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## List Coupons

Retrieve all coupons for a location.

**Method:** `GET`
**URL:** `/payments/coupon/list`
**Scope:** `payments/coupons.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/coupon/list?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "coupons": [
    {
      "_id": "coupon_abc123",
      "name": "Summer Sale 20%",
      "code": "SUMMER20",
      "discountType": "percentage",
      "discountValue": 20,
      "duration": "once",
      "productIds": ["prod_123", "prod_456"]
    }
  ]
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Create Coupon

Create a new promotional coupon. Note: this endpoint returns HTTP `201 Created` instead of the standard `200 OK`.

**Method:** `POST`
**URL:** `/payments/coupon`
**Scope:** `payments/coupons.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `name` | string | Yes | Coupon display name |
| `code` | string | Yes | Promotional code (what customers enter) |
| `discountType` | string | Yes | `"percentage"` or `"fixed"` |
| `discountValue` | number | Yes | Discount amount |
| `duration` | string | No | `"forever"`, `"multiple_months"`, or `"once"` |
| `durationInMonths` | number | No | Number of months (when duration is `"multiple_months"`) |
| `maxUses` | number | No | Maximum total uses allowed |
| `startDate` | string | No | When the coupon becomes active |
| `endDate` | string | No | When the coupon expires |
| `productIds` | array[string] | No | Product IDs the coupon applies to |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/payments/coupon' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Summer Sale 20%",
    "code": "SUMMER20",
    "discountType": "percentage",
    "discountValue": 20,
    "duration": "once",
    "productIds": ["prod_123", "prod_456"]
  }'
```

### Response Example

```json
{
  "_id": "coupon_abc123",
  "name": "Summer Sale 20%",
  "code": "SUMMER20",
  "discountType": "percentage",
  "discountValue": 20,
  "duration": "once",
  "productIds": ["prod_123", "prod_456"]
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Returns HTTP 201 on success.

---

## Fetch Coupon

Retrieve a coupon by its ID or promotional code.

**Method:** `GET`
**URL:** `/payments/coupon`
**Scope:** `payments/coupons.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `code` | string | No | Promotional code to look up |
| `couponId` | string | No | Coupon ID to look up |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/coupon?altId=loc_abc123&altType=location&code=SUMMER20' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "coupon_abc123",
  "name": "Summer Sale 20%",
  "code": "SUMMER20",
  "discountType": "percentage",
  "discountValue": 20,
  "duration": "once",
  "maxUses": 100,
  "startDate": "2026-06-01T00:00:00.000Z",
  "endDate": "2026-08-31T23:59:59.000Z",
  "productIds": ["prod_123", "prod_456"]
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Update Coupon

Modify an existing coupon's parameters.

**Method:** `PUT`
**URL:** `/payments/coupon`
**Scope:** `payments/coupons.write`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `couponId` | string | Yes | Coupon ID to update |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | No | Coupon display name |
| `discountType` | string | No | `"percentage"` or `"fixed"` |
| `discountValue` | number | No | Discount amount |
| `duration` | string | No | `"forever"`, `"multiple_months"`, or `"once"` |
| `durationInMonths` | number | No | Number of months |
| `maxUses` | number | No | Maximum total uses allowed |
| `startDate` | string | No | When the coupon becomes active |
| `endDate` | string | No | When the coupon expires |
| `productIds` | array[string] | No | Product IDs the coupon applies to |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/payments/coupon?altId=loc_abc123&altType=location&couponId=coupon_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Summer Sale 25%",
    "discountValue": 25,
    "endDate": "2026-09-30T23:59:59.000Z"
  }'
```

### Response Example

```json
{
  "_id": "coupon_abc123",
  "name": "Summer Sale 25%",
  "code": "SUMMER20",
  "discountType": "percentage",
  "discountValue": 25,
  "duration": "once",
  "endDate": "2026-09-30T23:59:59.000Z",
  "productIds": ["prod_123", "prod_456"]
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Delete Coupon

Permanently remove a coupon. This action cannot be undone.

**Method:** `DELETE`
**URL:** `/payments/coupon`
**Scope:** `payments/coupons.write`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `couponId` | string | Yes | Coupon ID to delete |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/payments/coupon?altId=loc_abc123&altType=location&couponId=coupon_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "success": true
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.
