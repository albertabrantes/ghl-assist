# Payments API — Orders

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Orders represent purchases made through GHL funnels, websites, forms, or invoices. This file covers order listing/retrieval, recording payments, the migration endpoint, order fulfillments, and order notes.

## Order Object

```json
{
  "_id": "order_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "contactId": "contact_123",
  "status": "completed",
  "amount": 299.99,
  "currency": "USD",
  "paymentMode": "live",
  "sourceType": "funnel",
  "funnelProductIds": ["prod_123"],
  "items": [],
  "fulfillmentStatus": "fulfilled",
  "createdAt": "2026-01-15T10:30:00.000Z",
  "updatedAt": "2026-01-15T10:35:00.000Z"
}
```

---

## List Orders

Retrieve a paginated list of orders with filtering. Orders represent purchases made through GHL funnels, websites, forms, or invoices.

**Method:** `GET`
**URL:** `/payments/orders`
**Scope:** `payments/orders.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `status` | string | No | Order status filter |
| `paymentMode` | string | No | Payment mode filter |
| `startAt` | string | No | Date range start |
| `endAt` | string | No | Date range end |
| `contactId` | string | No | Filter by GHL contact ID |
| `funnelProductIds` | string | No | Filter by funnel product IDs |
| `sourceType` | string | No | Filter by source type |
| `search` | string | No | Search term |
| `limit` | number | No | Page size |
| `offset` | number | No | Pagination offset |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/orders?altId=loc_abc123&altType=location&status=completed&limit=20&offset=0' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "orders": [
    {
      "_id": "order_abc123",
      "altId": "loc_abc123",
      "altType": "location",
      "contactId": "contact_123",
      "status": "completed",
      "amount": 299.99,
      "currency": "USD",
      "sourceType": "funnel",
      "createdAt": "2026-01-15T10:30:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Get Order by ID

Retrieve a specific order by its ID.

**Method:** `GET`
**URL:** `/payments/orders/{orderId}`
**Scope:** `payments/orders.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `orderId` | string | Yes | Order ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/orders/order_abc123?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "order_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "contactId": "contact_123",
  "status": "completed",
  "amount": 299.99,
  "currency": "USD",
  "paymentMode": "live",
  "sourceType": "funnel",
  "items": [],
  "fulfillmentStatus": "fulfilled",
  "createdAt": "2026-01-15T10:30:00.000Z",
  "updatedAt": "2026-01-15T10:35:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Record Order Payment

Record a payment for an order and update its status to Paid.

**Method:** `POST`
**URL:** `/payments/orders/{orderId}/record-payment`
**Scope:** `payments/orders.collectPayment`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `orderId` | string | Yes | Order ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `amount` | number | Yes | Payment amount |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/payments/orders/order_abc123/record-payment' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "amount": 299.99
  }'
```

### Response Example

```json
{
  "success": true,
  "order": {
    "_id": "order_abc123",
    "status": "completed"
  }
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Migrate Order Payment Status

Internal migration endpoint for updating order payment statuses. This is an internal/migration endpoint not intended for general developer use.

**Method:** `POST`
**URL:** `/payments/orders/migrate-order-ps`
**Scope:** None specified (internal endpoint)

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/payments/orders/migrate-order-ps' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. This is an internal/migration endpoint and is not intended for general developer use.

---

## Create Order Fulfillment

Record that an order has been fulfilled (partially or fully).

**Method:** `POST`
**URL:** `/payments/orders/{orderId}/fulfillments`
**Scope:** `payments/orders.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `orderId` | string | Yes | Order ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `trackingNumber` | string | No | Shipment tracking number |
| `carrier` | string | No | Shipping carrier name |
| `items` | array | No | Array of fulfilled items |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/payments/orders/order_abc123/fulfillments' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "trackingNumber": "1Z999AA10123456784",
    "carrier": "UPS",
    "items": []
  }'
```

### Response Example

```json
{
  "success": true,
  "fulfillment": {
    "_id": "ful_abc123",
    "orderId": "order_abc123",
    "trackingNumber": "1Z999AA10123456784",
    "carrier": "UPS",
    "createdAt": "2026-01-16T09:00:00.000Z"
  }
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## List Order Fulfillments

Get all fulfillment records for an order.

**Method:** `GET`
**URL:** `/payments/orders/{orderId}/fulfillments`
**Scope:** `payments/orders.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `orderId` | string | Yes | Order ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/orders/order_abc123/fulfillments?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "fulfillments": [
    {
      "_id": "ful_abc123",
      "orderId": "order_abc123",
      "trackingNumber": "1Z999AA10123456784",
      "carrier": "UPS",
      "items": [],
      "createdAt": "2026-01-16T09:00:00.000Z"
    }
  ]
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## List Order Notes

Retrieve all notes attached to a specific order.

**Method:** `GET`
**URL:** `/payments/orders/{orderId}/notes`
**Scope:** Not explicitly specified

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `orderId` | string | Yes | Order ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/orders/order_abc123/notes?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "notes": [
    {
      "_id": "note_abc123",
      "orderId": "order_abc123",
      "body": "Customer requested expedited shipping.",
      "createdAt": "2026-01-15T11:00:00.000Z"
    }
  ]
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.
