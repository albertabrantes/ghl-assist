# Invoices API — Schedules & Text2Pay

Recurring invoice schedule endpoints and Text2Pay. See [schemas.md](./schemas.md) for object schemas, scopes, auth, and rate limits.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Schedule endpoints follow a lifecycle pattern: Create (draft) → Activate → Active → Cancel/Complete.

| Endpoint | Method | Scope |
|----------|--------|-------|
| [Create Invoice Schedule](#create-invoice-schedule) | POST | invoices/schedule.write |
| [List Schedules](#list-schedules) | GET | invoices/schedule.readonly |
| [Get Schedule](#get-schedule) | GET | invoices/schedule.readonly |
| [Update Schedule](#update-schedule) | PUT | invoices/schedule.write |
| [Delete Schedule](#delete-schedule) | DELETE | invoices/schedule.write |
| [Update and Reschedule](#update-and-reschedule) | POST | invoices/schedule.write |
| [Activate Schedule](#activate-schedule) | POST | invoices/schedule.write |
| [Manage Auto Payment](#manage-auto-payment) | POST | invoices/schedule.write |
| [Cancel Schedule](#cancel-schedule) | POST | invoices/schedule.write |
| [Create and Send Text2Pay](#create-and-send-text2pay) | POST | invoices.write |

---

## Create Invoice Schedule

Create a new recurring invoice schedule. Schedules are created in `draft` status and must be activated via the [Activate Schedule](#activate-schedule) or [Update and Reschedule](#update-and-reschedule) endpoints.

**Method:** `POST`
**URL:** `/invoices/schedule`
**Scope:** `invoices/schedule.write`

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `name` | string | Yes | Schedule name |
| `contactDetails` | object | Yes | Contact info (same structure as invoice `contactDetails`) |
| `businessDetails` | object | No | Business info (same structure as invoice `businessDetails`) |
| `invoiceItems` | array | Yes | Line items for recurring invoices |
| `currency` | string | Yes | ISO currency code |
| `frequency` | string | Yes | `weekly`, `monthly`, `quarterly`, `yearly`, or `custom` |
| `startDate` | string | Yes | Start date in `YYYY-MM-DD` format |
| `endCondition` | string | No | End condition: cycle count or specific date |
| `autoPayment` | boolean | No | Whether to auto-charge the contact |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/schedule' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Monthly Retainer",
    "contactDetails": {
      "id": "contact_xyz789",
      "name": "Jane Smith",
      "email": "jane@example.com"
    },
    "invoiceItems": [
      {
        "name": "Monthly Retainer",
        "currency": "USD",
        "qty": 1,
        "amount": 1500
      }
    ],
    "currency": "USD",
    "frequency": "monthly",
    "startDate": "2026-03-01"
  }'
```

### Response Example

```json
{
  "_id": "sched_abc123",
  "status": "draft",
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Monthly Retainer",
  "frequency": "monthly",
  "startDate": "2026-03-01",
  "createdAt": "2026-02-23T10:00:00.000Z",
  "updatedAt": "2026-02-23T10:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Request body fields are **inferred**.

---

## List Schedules

Retrieve all invoice schedules for a location.

**Method:** `GET`
**URL:** `/invoices/schedule`
**Scope:** `invoices/schedule.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/schedule?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "schedules": [
    {
      "_id": "sched_abc123",
      "status": "active",
      "name": "Monthly Retainer",
      "frequency": "monthly",
      "startDate": "2026-03-01",
      "createdAt": "2026-02-23T10:00:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Get Schedule

Retrieve a single invoice schedule by its ID.

**Method:** `GET`
**URL:** `/invoices/schedule/{scheduleId}`
**Scope:** `invoices/schedule.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `scheduleId` | string | Yes | Schedule ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/schedule/sched_abc123?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "sched_abc123",
  "status": "active",
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Monthly Retainer",
  "frequency": "monthly",
  "startDate": "2026-03-01",
  "contactDetails": {
    "id": "contact_xyz789",
    "name": "Jane Smith",
    "email": "jane@example.com"
  },
  "invoiceItems": [
    {
      "name": "Monthly Retainer",
      "currency": "USD",
      "qty": 1,
      "amount": 1500
    }
  ],
  "currency": "USD",
  "autoPayment": false,
  "createdAt": "2026-02-23T10:00:00.000Z",
  "updatedAt": "2026-02-23T10:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Update Schedule

Update an existing invoice schedule's fields.

**Method:** `PUT`
**URL:** `/invoices/schedule/{scheduleId}`
**Scope:** `invoices/schedule.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `scheduleId` | string | Yes | Schedule ID |

### Request Body

Accepts the same fields as [Create Invoice Schedule](#create-invoice-schedule). Only provided fields are updated. Does not reschedule -- use [Update and Reschedule](#update-and-reschedule) to update and immediately reschedule.

### Request Example

```bash
curl -X PUT 'https://services.leadconnectorhq.com/invoices/schedule/sched_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Updated Monthly Retainer",
    "invoiceItems": [
      {
        "name": "Monthly Retainer",
        "currency": "USD",
        "qty": 1,
        "amount": 2000
      }
    ]
  }'
```

### Response Example

```json
{
  "_id": "sched_abc123",
  "name": "Updated Monthly Retainer",
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Delete Schedule

Delete an invoice schedule.

**Method:** `DELETE`
**URL:** `/invoices/schedule/{scheduleId}`
**Scope:** `invoices/schedule.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `scheduleId` | string | Yes | Schedule ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/invoices/schedule/sched_abc123?altId=loc_abc123&altType=location' \
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

## Update and Reschedule

Update a recurring invoice schedule and immediately reschedule it. Unlike [Update Schedule](#update-schedule), this endpoint applies changes and reactivates the schedule in one operation.

**Method:** `POST`
**URL:** `/invoices/schedule/{scheduleId}/updateAndSchedule`
**Scope:** `invoices/schedule.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `scheduleId` | string | Yes | Schedule ID |

### Request Body

Accepts the same fields as [Create Invoice Schedule](#create-invoice-schedule).

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/schedule/sched_abc123/updateAndSchedule' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Updated Monthly Retainer",
    "frequency": "monthly",
    "startDate": "2026-04-01",
    "invoiceItems": [
      {
        "name": "Monthly Retainer",
        "currency": "USD",
        "qty": 1,
        "amount": 2000
      }
    ]
  }'
```

### Response Example

```json
{
  "_id": "sched_abc123",
  "status": "scheduled",
  "name": "Updated Monthly Retainer",
  "frequency": "monthly",
  "startDate": "2026-04-01",
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Activate Schedule

Start sending invoices according to the schedule. Transitions a `draft` schedule to `active` or `scheduled` status.

**Method:** `POST`
**URL:** `/invoices/schedule/{scheduleId}/schedule`
**Scope:** `invoices/schedule.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `scheduleId` | string | Yes | Schedule ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/schedule/sched_abc123/schedule' \
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
  "_id": "sched_abc123",
  "status": "active",
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Manage Auto Payment

Toggle or configure automatic payment collection for a scheduled invoice.

**Method:** `POST`
**URL:** `/invoices/schedule/{scheduleId}/auto-payment`
**Scope:** `invoices/schedule.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `scheduleId` | string | Yes | Schedule ID |

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `autoPayment` | boolean | Yes | `true` to enable auto-charge, `false` to disable |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/schedule/sched_abc123/auto-payment' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "autoPayment": true
  }'
```

### Response Example

```json
{
  "_id": "sched_abc123",
  "autoPayment": true,
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API. Request body fields are **inferred**.

---

## Cancel Schedule

Cancel a recurring invoice schedule. No further invoices will be generated.

**Method:** `POST`
**URL:** `/invoices/schedule/{scheduleId}/cancel`
**Scope:** `invoices/schedule.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `scheduleId` | string | Yes | Schedule ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/schedule/sched_abc123/cancel' \
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
  "_id": "sched_abc123",
  "status": "canceled",
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Create and Send Text2Pay

Create or update a Text2Pay invoice and send a payment link via SMS to the contact.

**Method:** `POST`
**URL:** `/invoices/text2pay`
**Scope:** `invoices.write`

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `contactId` | string | Yes | GHL Contact ID |
| `amount` | number | Yes | Payment amount |
| `currency` | string | Yes | ISO currency code (e.g., `"USD"`) |
| `name` | string | No | Invoice/payment name |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/text2pay' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "contactId": "contact_xyz789",
    "amount": 500,
    "currency": "USD",
    "name": "Service Payment"
  }'
```

### Response Example

```json
{
  "_id": "inv_t2p_abc123",
  "status": "sent",
  "amount": 500,
  "currency": "USD",
  "contactId": "contact_xyz789",
  "name": "Service Payment",
  "createdAt": "2026-02-23T10:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.
