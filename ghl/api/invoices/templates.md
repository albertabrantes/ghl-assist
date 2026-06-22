# Invoices API — Invoice Templates

Reusable invoice template endpoints. See [schemas.md](./schemas.md) for object schemas, scopes, auth, and rate limits.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

| Endpoint | Method | Scope |
|----------|--------|-------|
| [Create Template](#create-template) | POST | invoices/template.write |
| [List Templates](#list-templates) | GET | invoices/template.readonly |
| [Get Template](#get-template) | GET | invoices/template.readonly |
| [Update Template](#update-template) | PUT | invoices/template.write |
| [Delete Template](#delete-template) | DELETE | invoices/template.write |
| [Update Template Late Fees Config](#update-template-late-fees-config) | PATCH | invoices/template.write |
| [Update Template Payment Methods Config](#update-template-payment-methods-config) | PATCH | invoices/template.write |

---

## Create Template

Create a reusable invoice template with pre-set business details, items, payment methods, and late fee policies.

**Method:** `POST`
**URL:** `/invoices/template`
**Scope:** `invoices/template.write`

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `name` | string | Yes | Template name |
| `currency` | string | Yes | ISO currency code (e.g., `"USD"`) |
| `businessDetails` | object | No | Business info -- `name`, `address`, `phoneNo`, `website`, `logoUrl` |
| `invoiceItems` | array | No | Default line items for invoices created from this template |
| `title` | string | No | Display title (default: `"INVOICE"`) |
| `discount` | object | No | Default discount configuration |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/template' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Standard Consulting Template",
    "currency": "USD",
    "businessDetails": {
      "name": "Acme Agency",
      "address": "123 Main St, Dallas, TX"
    },
    "invoiceItems": [
      {
        "name": "Consulting Hour",
        "currency": "USD",
        "qty": 1,
        "amount": 150
      }
    ],
    "title": "INVOICE"
  }'
```

### Response Example

```json
{
  "_id": "tmpl_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Standard Consulting Template",
  "currency": "USD",
  "businessDetails": {
    "name": "Acme Agency",
    "address": "123 Main St, Dallas, TX"
  },
  "invoiceItems": [
    {
      "name": "Consulting Hour",
      "currency": "USD",
      "qty": 1,
      "amount": 150
    }
  ],
  "title": "INVOICE",
  "createdAt": "2026-02-23T10:00:00.000Z",
  "updatedAt": "2026-02-23T10:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## List Templates

Retrieve all invoice templates for a location.

**Method:** `GET`
**URL:** `/invoices/template`
**Scope:** `invoices/template.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/template?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "templates": [
    {
      "_id": "tmpl_abc123",
      "name": "Standard Consulting Template",
      "currency": "USD",
      "createdAt": "2026-02-23T10:00:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Get Template

Retrieve a single invoice template by its ID.

**Method:** `GET`
**URL:** `/invoices/template/{templateId}`
**Scope:** `invoices/template.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `templateId` | string | Yes | Template ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/template/tmpl_abc123?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "tmpl_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Standard Consulting Template",
  "currency": "USD",
  "businessDetails": {
    "name": "Acme Agency",
    "address": "123 Main St, Dallas, TX"
  },
  "invoiceItems": [
    {
      "name": "Consulting Hour",
      "currency": "USD",
      "qty": 1,
      "amount": 150
    }
  ],
  "title": "INVOICE",
  "createdAt": "2026-02-23T10:00:00.000Z",
  "updatedAt": "2026-02-23T10:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Update Template

Update an existing invoice template.

**Method:** `PUT`
**URL:** `/invoices/template/{templateId}`
**Scope:** `invoices/template.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `templateId` | string | Yes | Template ID |

### Request Body

Accepts the same fields as [Create Template](#create-template). Only provided fields are updated.

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/invoices/template/tmpl_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Updated Consulting Template",
    "invoiceItems": [
      {
        "name": "Consulting Hour",
        "currency": "USD",
        "qty": 1,
        "amount": 200
      }
    ]
  }'
```

### Response Example

```json
{
  "_id": "tmpl_abc123",
  "name": "Updated Consulting Template",
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Delete Template

Delete an invoice template.

**Method:** `DELETE`
**URL:** `/invoices/template/{templateId}`
**Scope:** `invoices/template.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `templateId` | string | Yes | Template ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/invoices/template/tmpl_abc123?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "succeeded": true
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Update Template Late Fees Config

Configure late fee settings on an invoice template.

**Method:** `PATCH`
**URL:** `/invoices/template/{templateId}/late-fees-configuration`
**Scope:** Not explicitly specified (likely requires `invoices/template.write`)

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `templateId` | string | Yes | Template ID |

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `lateFeeConfig` | object | Yes | Late fee configuration object (fields inferred -- may include `enabled`, `type`, `value`, `frequency`) |

### Request Example

```bash
curl -X PATCH 'https://services.leadconnectorhq.com/invoices/template/tmpl_abc123/late-fees-configuration' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "lateFeeConfig": {
      "enabled": true,
      "type": "percentage",
      "value": 1.5,
      "frequency": "monthly"
    }
  }'
```

### Response Example

```json
{
  "succeeded": true
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Request body fields are **inferred**.

---

## Update Template Payment Methods Config

Configure accepted payment methods on an invoice template.

**Method:** `PATCH`
**URL:** `/invoices/template/{templateId}/payment-methods-configuration`
**Scope:** Not explicitly specified (likely requires `invoices/template.write`)

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `templateId` | string | Yes | Template ID |

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `paymentMethodsConfig` | object | Yes | Payment methods configuration (fields inferred -- may include `creditCard`, `bankTransfer`, `cash`, `check`) |

### Request Example

```bash
curl -X PATCH 'https://services.leadconnectorhq.com/invoices/template/tmpl_abc123/payment-methods-configuration' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "paymentMethodsConfig": {
      "creditCard": true,
      "bankTransfer": true,
      "cash": false,
      "check": false
    }
  }'
```

### Response Example

```json
{
  "succeeded": true
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Request body fields are **inferred**.
