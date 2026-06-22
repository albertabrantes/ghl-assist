# Payments API — Transactions

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Transactions represent individual payment events -- charges, refunds, or transfers. This file covers listing and retrieving transactions.

## Transaction Object

```json
{
  "_id": "txn_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "contactId": "contact_123",
  "orderId": "order_abc123",
  "subscriptionId": "sub_abc123",
  "amount": 299.99,
  "currency": "USD",
  "status": "succeeded",
  "paymentMode": "live",
  "entityId": "entity_123",
  "entitySourceType": "funnel",
  "createdAt": "2026-01-15T10:30:00.000Z",
  "updatedAt": "2026-01-15T10:30:00.000Z"
}
```

---

## List Transactions

Retrieve a paginated list of transactions with filtering. Transactions represent individual payment events -- charges, refunds, or transfers.

**Method:** `GET`
**URL:** `/payments/transactions`
**Scope:** `payments/transactions.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `status` | string | No | Transaction status filter |
| `paymentMode` | string | No | Payment mode filter |
| `startAt` | string | No | Date range start |
| `endAt` | string | No | Date range end |
| `contactId` | string | No | Filter by contact ID |
| `subscriptionId` | string | No | Filter by subscription ID |
| `entityId` | string | No | Filter by entity ID |
| `entitySourceType` | string | No | Filter by entity source type |
| `search` | string | No | Search term |
| `limit` | number | No | Page size |
| `offset` | number | No | Pagination offset |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/transactions?altId=loc_abc123&altType=location&limit=50' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "transactions": [
    {
      "_id": "txn_abc123",
      "altId": "loc_abc123",
      "altType": "location",
      "contactId": "contact_123",
      "amount": 299.99,
      "currency": "USD",
      "status": "succeeded",
      "paymentMode": "live",
      "createdAt": "2026-01-15T10:30:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Get Transaction by ID

Retrieve a specific transaction by its ID.

**Method:** `GET`
**URL:** `/payments/transactions/{transactionId}`
**Scope:** `payments/transactions.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `transactionId` | string | Yes | Transaction ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/payments/transactions/txn_abc123?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "txn_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "contactId": "contact_123",
  "orderId": "order_abc123",
  "subscriptionId": "sub_abc123",
  "amount": 299.99,
  "currency": "USD",
  "status": "succeeded",
  "paymentMode": "live",
  "entityId": "entity_123",
  "entitySourceType": "funnel",
  "createdAt": "2026-01-15T10:30:00.000Z",
  "updatedAt": "2026-01-15T10:30:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.
