# Invoices API — Estimates & Estimate Templates

Estimate (quote/proposal) endpoints and estimate templates. Estimates mirror invoice endpoints closely in structure and field naming. See [schemas.md](./schemas.md) for object schemas, scopes, auth, and rate limits.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

| Endpoint | Method | Scope |
|----------|--------|-------|
| [Create Estimate](#create-estimate) | POST | invoices/estimate.write |
| [Update Estimate](#update-estimate) | PUT | invoices/estimate.write |
| [Delete Estimate](#delete-estimate) | DELETE | invoices/estimate.write |
| [Generate Estimate Number](#generate-estimate-number) | GET | invoices/estimate.readonly |
| [Send Estimate](#send-estimate) | POST | invoices/estimate.write |
| [Create Invoice from Estimate](#create-invoice-from-estimate) | POST | invoices/estimate.write |
| [List Estimates](#list-estimates) | GET | invoices/estimate.readonly |
| [Update Estimate Last Visited At](#update-estimate-last-visited-at) | PATCH | -- |
| [List Estimate Templates](#list-estimate-templates) | GET | invoices/estimate.readonly |
| [Create Estimate Template](#create-estimate-template) | POST | invoices/estimate.write |
| [Update Estimate Template](#update-estimate-template) | PUT | invoices/estimate.write |
| [Delete Estimate Template](#delete-estimate-template) | DELETE | invoices/estimate.write |
| [Preview Estimate Template](#preview-estimate-template) | GET | invoices/estimate.readonly |

---

## Create Estimate

Create a new estimate (quote/proposal) for a contact. Estimates share a nearly identical structure to invoices and can be converted into invoices via the [Create Invoice from Estimate](#create-invoice-from-estimate) endpoint.

**Method:** `POST`
**URL:** `/invoices/estimate`
**Scope:** `invoices/estimate.write`

### Request Body (inferred -- mirrors Create Invoice schema)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `name` | string | No | Estimate name/label |
| `contactDetails` | object | Yes | Contact info -- must include `id` at minimum |
| `businessDetails` | object | No | Business info |
| `currency` | string | Yes | ISO currency code |
| `issueDate` | string | Yes | `"YYYY-MM-DD"` format |
| `dueDate` | string | Yes | `"YYYY-MM-DD"` format |
| `invoiceItems` | array | Yes | Array of line items |
| `discount` | object | No | Discount configuration |
| `title` | string | No | Display title (default: `"ESTIMATE"`) |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/estimate' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Website Redesign Estimate",
    "currency": "USD",
    "issueDate": "2026-02-23",
    "dueDate": "2026-03-23",
    "contactDetails": {
      "id": "contact_xyz789",
      "name": "Jane Smith",
      "email": "jane@example.com"
    },
    "invoiceItems": [
      {
        "name": "Website Redesign",
        "currency": "USD",
        "qty": 1,
        "amount": 5000
      }
    ],
    "title": "ESTIMATE"
  }'
```

### Response Example

```json
{
  "_id": "est_abc123",
  "status": "draft",
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Website Redesign Estimate",
  "currency": "USD",
  "issueDate": "2026-02-23",
  "dueDate": "2026-03-23",
  "contactDetails": {
    "id": "contact_xyz789",
    "name": "Jane Smith",
    "email": "jane@example.com"
  },
  "invoiceItems": [
    {
      "name": "Website Redesign",
      "currency": "USD",
      "qty": 1,
      "amount": 5000
    }
  ],
  "total": 5000,
  "title": "ESTIMATE",
  "createdAt": "2026-02-23T10:00:00.000Z",
  "updatedAt": "2026-02-23T10:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Request body fields are **inferred** from invoice schema patterns.

---

## Update Estimate

Update an existing estimate's fields.

**Method:** `PUT`
**URL:** `/invoices/estimate/{estimateId}`
**Scope:** `invoices/estimate.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `estimateId` | string | Yes | Estimate ID |

### Request Body

Accepts the same fields as [Create Estimate](#create-estimate). Only provided fields are updated.

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/invoices/estimate/est_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "dueDate": "2026-04-23",
    "invoiceItems": [
      {
        "name": "Website Redesign",
        "currency": "USD",
        "qty": 1,
        "amount": 5500
      }
    ]
  }'
```

### Response Example

```json
{
  "_id": "est_abc123",
  "dueDate": "2026-04-23",
  "total": 5500,
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Delete Estimate

Delete an estimate.

**Method:** `DELETE`
**URL:** `/invoices/estimate/{estimateId}`
**Scope:** `invoices/estimate.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `estimateId` | string | Yes | Estimate ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/invoices/estimate/est_abc123?altId=loc_abc123&altType=location' \
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

## Generate Estimate Number

Retrieve the next sequential estimate number for a location.

**Method:** `GET`
**URL:** `/invoices/estimate/number/generate`
**Scope:** `invoices/estimate.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/estimate/number/generate?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "estimateNumber": "5"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Send Estimate

Send an estimate to the contact for review via email.

**Method:** `POST`
**URL:** `/invoices/estimate/{estimateId}/send`
**Scope:** `invoices/estimate.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `estimateId` | string | Yes | Estimate ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/estimate/est_abc123/send' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location"
  }'
```

### Response Example

```json
{
  "_id": "est_abc123",
  "status": "sent",
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Create Invoice from Estimate

Convert an approved estimate into a new invoice. The invoice is pre-populated with the estimate's line items, contact details, and business details. The original estimate remains unchanged.

**Method:** `POST`
**URL:** `/invoices/estimate/{estimateId}/invoice`
**Scope:** `invoices/estimate.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `estimateId` | string | Yes | Estimate ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/estimate/est_abc123/invoice' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location"
  }'
```

### Response Example

```json
{
  "_id": "inv_new_from_est",
  "status": "draft",
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Website Redesign Estimate",
  "currency": "USD",
  "contactDetails": {
    "id": "contact_xyz789",
    "name": "Jane Smith",
    "email": "jane@example.com"
  },
  "invoiceItems": [
    {
      "name": "Website Redesign",
      "currency": "USD",
      "qty": 1,
      "amount": 5000
    }
  ],
  "total": 5000,
  "title": "INVOICE",
  "amountDue": 5000,
  "createdAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## List Estimates

Retrieve a paginated list of estimates for a location.

**Method:** `GET`
**URL:** `/invoices/estimate/list`
**Scope:** `invoices/estimate.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `status` | string | No | Filter by estimate status |
| `contactId` | string | No | Filter by contact ID |
| `search` | string | No | Search term |
| `limit` | number | No | Page size |
| `offset` | number | No | Pagination offset |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/estimate/list?altId=loc_abc123&altType=location&limit=20&offset=0' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "estimates": [
    {
      "_id": "est_abc123",
      "status": "sent",
      "name": "Website Redesign Estimate",
      "currency": "USD",
      "total": 5000,
      "contactDetails": {
        "id": "contact_xyz789",
        "name": "Jane Smith"
      },
      "createdAt": "2026-02-23T10:00:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Query parameters beyond `altId`/`altType` are **inferred** from invoice list patterns.

---

## Update Estimate Last Visited At

Update the last visited timestamp for estimate analytics/tracking.

**Method:** `PATCH`
**URL:** `/invoices/estimate/stats/last-visited-at`
**Scope:** Not explicitly specified

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X PATCH 'https://services.leadconnectorhq.com/invoices/estimate/stats/last-visited-at' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location"
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

## List Estimate Templates

Retrieve all estimate templates for a location.

**Method:** `GET`
**URL:** `/invoices/estimate/template`
**Scope:** `invoices/estimate.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/estimate/template?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "templates": [
    {
      "_id": "est_tmpl_abc123",
      "name": "Standard Estimate Template",
      "currency": "USD",
      "createdAt": "2026-02-23T10:00:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Create Estimate Template

Create a reusable estimate template.

**Method:** `POST`
**URL:** `/invoices/estimate/template`
**Scope:** `invoices/estimate.write`

### Request Body (inferred -- mirrors invoice template schema)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `name` | string | Yes | Template name |
| `currency` | string | Yes | ISO currency code |
| `businessDetails` | object | No | Business info |
| `invoiceItems` | array | No | Default line items |
| `title` | string | No | Display title (default: `"ESTIMATE"`) |
| `discount` | object | No | Default discount configuration |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/estimate/template' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Standard Estimate Template",
    "currency": "USD",
    "businessDetails": {
      "name": "Acme Agency",
      "address": "123 Main St, Dallas, TX"
    },
    "invoiceItems": [
      {
        "name": "Consulting",
        "currency": "USD",
        "qty": 1,
        "amount": 200
      }
    ],
    "title": "ESTIMATE"
  }'
```

### Response Example

```json
{
  "_id": "est_tmpl_abc123",
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Standard Estimate Template",
  "currency": "USD",
  "businessDetails": {
    "name": "Acme Agency",
    "address": "123 Main St, Dallas, TX"
  },
  "invoiceItems": [
    {
      "name": "Consulting",
      "currency": "USD",
      "qty": 1,
      "amount": 200
    }
  ],
  "title": "ESTIMATE",
  "createdAt": "2026-02-23T10:00:00.000Z",
  "updatedAt": "2026-02-23T10:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Request body fields are **inferred** from invoice template patterns.

---

## Update Estimate Template

Update an existing estimate template.

**Method:** `PUT`
**URL:** `/invoices/estimate/template/{templateId}`
**Scope:** `invoices/estimate.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `templateId` | string | Yes | Estimate template ID |

### Request Body

Accepts the same fields as [Create Estimate Template](#create-estimate-template). Only provided fields are updated.

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/invoices/estimate/template/est_tmpl_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Updated Estimate Template",
    "invoiceItems": [
      {
        "name": "Consulting",
        "currency": "USD",
        "qty": 1,
        "amount": 250
      }
    ]
  }'
```

### Response Example

```json
{
  "_id": "est_tmpl_abc123",
  "name": "Updated Estimate Template",
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Delete Estimate Template

Delete an estimate template.

**Method:** `DELETE`
**URL:** `/invoices/estimate/template/{templateId}`
**Scope:** `invoices/estimate.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `templateId` | string | Yes | Estimate template ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/invoices/estimate/template/est_tmpl_abc123?altId=loc_abc123&altType=location' \
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

## Preview Estimate Template

Preview the rendered output of an estimate template.

**Method:** `GET`
**URL:** `/invoices/estimate/template/preview`
**Scope:** `invoices/estimate.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `templateId` | string | Yes | Estimate template ID to preview |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/estimate/template/preview?altId=loc_abc123&altType=location&templateId=est_tmpl_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "est_tmpl_abc123",
  "name": "Standard Estimate Template",
  "currency": "USD",
  "businessDetails": {
    "name": "Acme Agency",
    "address": "123 Main St, Dallas, TX"
  },
  "invoiceItems": [
    {
      "name": "Consulting",
      "currency": "USD",
      "qty": 1,
      "amount": 200
    }
  ],
  "title": "ESTIMATE",
  "total": 200,
  "totalSummary": {
    "subTotal": 200,
    "discount": 0
  }
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Query parameters are **inferred**.
