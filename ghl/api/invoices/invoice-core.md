# Invoices API — Invoice Core

Core invoice lifecycle endpoints. See [schemas.md](./schemas.md) for object schemas, scopes, auth, and rate limits.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

| Endpoint | Method | Scope |
|----------|--------|-------|
| [Generate Invoice Number](#generate-invoice-number) | GET | invoices.readonly |
| [Create Invoice](#create-invoice) | POST | invoices.write |
| [List Invoices](#list-invoices) | GET | invoices.readonly |
| [Get Invoice](#get-invoice) | GET | invoices.readonly |
| [Update Invoice](#update-invoice) | PUT | invoices.write |
| [Delete Invoice](#delete-invoice) | DELETE | invoices.write |
| [Void Invoice](#void-invoice) | POST | invoices.write |
| [Send Invoice](#send-invoice) | POST | invoices.write |
| [Record Manual Payment](#record-manual-payment) | POST | invoices.write |
| [Update Invoice Late Fees Config](#update-invoice-late-fees-config) | PATCH | invoices.write |
| [Update Invoice Last Visited At](#update-invoice-last-visited-at) | PATCH | -- |

---

## Generate Invoice Number

Retrieve the next sequential invoice number for a location.

**Method:** `GET`
**URL:** `/invoices/generate-invoice-number`
**Scope:** `invoices.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID (sub-account ID) |
| `altType` | string | Yes | Entity type, typically `"location"` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/generate-invoice-number?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "invoiceNumber": "20"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Create Invoice

Create a new invoice for a contact within a location.

**Method:** `POST`
**URL:** `/invoices/`
**Scope:** `invoices.write`

### Request Body (inferred from response schema and webhook payloads)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `name` | string | No | Invoice name/label |
| `contactDetails` | object | Yes | Contact info -- must include `id` (GHL contact ID) at minimum; also supports `name`, `email`, `phoneNo`, `address`, `companyName`, `additionalEmails`, `customFields` |
| `businessDetails` | object | No | Business info -- `name`, `address`, `phoneNo`, `website`, `logoUrl`, `customValues` |
| `invoiceNumber` | string | No | Custom invoice number (auto-generated if omitted -- use Generate Invoice Number endpoint) |
| `currency` | string | Yes | ISO currency code (e.g., `"USD"`) |
| `issueDate` | string | Yes | `"YYYY-MM-DD"` format |
| `dueDate` | string | Yes | `"YYYY-MM-DD"` format |
| `invoiceItems` | array | Yes | Array of line items (each with `productId`, `priceId`, `name`, `currency`, `qty`, `amount`, `taxes`) |
| `discount` | object | No | `{ "type": "percentage"\|"fixed", "value": number }` |
| `title` | string | No | Display title (default: `"INVOICE"`) |
| `liveMode` | boolean | No | `true` for real transactions, `false` for test mode |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "name": "Website Design Invoice",
    "currency": "USD",
    "issueDate": "2026-02-23",
    "dueDate": "2026-03-23",
    "contactDetails": {
      "id": "contact_xyz789",
      "name": "Jane Smith",
      "email": "jane@example.com",
      "phoneNo": "+15551234567",
      "companyName": "Smith Co.",
      "address": {
        "countryCode": "US",
        "addressLine1": "456 Oak Ave",
        "city": "Austin",
        "state": "TX",
        "postalCode": "73301"
      }
    },
    "businessDetails": {
      "name": "Acme Agency",
      "address": "123 Main St, Dallas, TX",
      "phoneNo": "+12145551234",
      "website": "https://acmeagency.com",
      "logoUrl": "https://acmeagency.com/logo.png"
    },
    "invoiceItems": [
      {
        "name": "Website Design",
        "productId": "prod_abc",
        "priceId": "price_abc",
        "currency": "USD",
        "qty": 1,
        "amount": 2500,
        "taxes": []
      }
    ],
    "discount": {
      "type": "percentage",
      "value": 10
    },
    "title": "INVOICE",
    "liveMode": true
  }'
```

### Response Example

```json
{
  "_id": "inv_00000000000000order00001",
  "status": "draft",
  "liveMode": true,
  "amountPaid": 0,
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Website Design Invoice",
  "businessDetails": {
    "name": "Acme Agency",
    "address": "123 Main St, Dallas, TX",
    "phoneNo": "+12145551234",
    "website": "https://acmeagency.com",
    "logoUrl": "https://acmeagency.com/logo.png",
    "customValues": []
  },
  "invoiceNumber": "20",
  "currency": "USD",
  "contactDetails": {
    "id": "contact_xyz789",
    "name": "Jane Smith",
    "email": "jane@example.com",
    "phoneNo": "+15551234567",
    "companyName": "Smith Co.",
    "address": {
      "countryCode": "US",
      "addressLine1": "456 Oak Ave",
      "city": "Austin",
      "state": "TX",
      "postalCode": "73301"
    },
    "additionalEmails": [],
    "customFields": []
  },
  "issueDate": "2026-02-23",
  "dueDate": "2026-03-23",
  "discount": {
    "type": "percentage",
    "value": 10
  },
  "invoiceItems": [
    {
      "_id": "item_abc123",
      "productId": "prod_abc",
      "priceId": "price_abc",
      "currency": "USD",
      "name": "Website Design",
      "qty": 1,
      "amount": 2500,
      "taxes": []
    }
  ],
  "total": 2250,
  "title": "INVOICE",
  "amountDue": 2250,
  "createdAt": "2026-02-23T10:00:00.000Z",
  "updatedAt": "2026-02-23T10:00:00.000Z",
  "totalSummary": {
    "subTotal": 2500,
    "discount": 250
  }
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## List Invoices

Retrieve a paginated list of invoices filtered by location and optional criteria.

**Method:** `GET`
**URL:** `/invoices/`
**Scope:** `invoices.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `status` | string | No | Filter by status: `draft`, `sent`, `paid`, `void`, `partially_paid`, `overdue` |
| `contactId` | string | No | Filter by contact ID |
| `search` | string | No | Search term |
| `startAt` | string | No | Date range start |
| `endAt` | string | No | Date range end |
| `limit` | number | No | Page size |
| `offset` | number | No | Pagination offset |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/?altId=loc_abc123&altType=location&status=sent&limit=20&offset=0' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "invoices": [
    {
      "_id": "inv_00000000000000order00001",
      "status": "sent",
      "name": "Website Design Invoice",
      "invoiceNumber": "20",
      "currency": "USD",
      "total": 2250,
      "amountDue": 2250,
      "amountPaid": 0,
      "issueDate": "2026-02-23",
      "dueDate": "2026-03-23",
      "contactDetails": {
        "id": "contact_xyz789",
        "name": "Jane Smith"
      },
      "createdAt": "2026-02-23T10:00:00.000Z",
      "updatedAt": "2026-02-23T10:00:00.000Z"
    }
  ],
  "total": 1
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Get Invoice

Retrieve a single invoice by its ID.

**Method:** `GET`
**URL:** `/invoices/{invoiceId}`
**Scope:** `invoices.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `invoiceId` | string | Yes | Invoice ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X GET 'https://services.leadconnectorhq.com/invoices/inv_6578278e?altId=loc_abc123&altType=location' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "_id": "inv_00000000000000order00001",
  "status": "sent",
  "liveMode": true,
  "amountPaid": 0,
  "altId": "loc_abc123",
  "altType": "location",
  "name": "Website Design Invoice",
  "businessDetails": {
    "name": "Acme Agency",
    "address": "123 Main St, Dallas, TX",
    "phoneNo": "+12145551234",
    "website": "https://acmeagency.com",
    "logoUrl": "https://acmeagency.com/logo.png",
    "customValues": []
  },
  "invoiceNumber": "20",
  "currency": "USD",
  "contactDetails": {
    "id": "contact_xyz789",
    "name": "Jane Smith",
    "email": "jane@example.com",
    "phoneNo": "+15551234567"
  },
  "issueDate": "2026-02-23",
  "dueDate": "2026-03-23",
  "invoiceItems": [
    {
      "_id": "item_abc123",
      "name": "Website Design",
      "qty": 1,
      "amount": 2500
    }
  ],
  "total": 2250,
  "title": "INVOICE",
  "amountDue": 2250,
  "createdAt": "2026-02-23T10:00:00.000Z",
  "updatedAt": "2026-02-23T10:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Update Invoice

Update an existing invoice's fields. Despite the API description, this is **not a partial update** — see the request body section below.

**Method:** `PUT`
**URL:** `/invoices/{invoiceId}`
**Scope:** `invoices.write`
**API Version:** `2023-02-21` (older `2021-07-28` also accepted, but use the current version)

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `invoiceId` | string | Yes | The internal MongoDB `_id` of the invoice. **NOT the human-readable `invoiceNumber`.** Sending an `invoiceNumber` here returns HTTP 422 `"invoiceId must be a mongodb id"`. Resolve via [List Invoices](#list-invoices) with `search={number}` first if you only have the human-readable number — see the gotcha below. |

### ⚠️ Required body — use carry-over, not cherry-pick

The endpoint claims "only provided fields are updated" but in practice **PUT enforces the full schema and rejects bodies that omit nested objects which exist on the live invoice**. A body with only the docs-listed required fields (`altId, altType, name, currency, invoiceItems, issueDate, dueDate`) returns:

```json
{
  "statusCode": 422,
  "message": [
    "businessDetails should not be empty",
    "contactDetails should not be empty",
    "discount should not be empty"
  ]
}
```

The "required" fields per the official docs are 7 (the ones listed above), but if the live invoice has `businessDetails`, `contactDetails`, `discount`, etc. populated, you must carry them over too — sending an empty/missing nested object is rejected.

**The reliable pattern is carry-over:**

1. `GET /invoices/{_id}` to fetch the live invoice
2. **Spread** the entire response into the PUT body
3. **Strip** server-managed meta fields the PUT contract rejects (see strip list below)
4. **Override** only the fields you actually want to change (typically `name`)
5. **Normalize** `issueDate` / `dueDate` to `YYYY-MM-DD`
6. PUT the full body

This way, whatever nested objects the invoice has — `businessDetails`, `contactDetails`, `discount`, `paymentSchedule`, `tipsConfiguration`, `attachments`, `miscellaneousCharges`, etc. — are preserved unchanged.

### Strip list (server-managed fields that GET returns but PUT rejects or shouldn't change)

```
_id, companyId, createdAt, updatedAt, __v, externalSources, lastVisitedAt, opportunityId, traceId
```

Strip these before PUT. Stripping these and spreading everything else from the GET response produces a 200 response. Other GET-only fields like `amountDue`, `amountPaid`, `status`, `total`, `totalSummary`, `invoiceTotal`, `lastPaidAt`, `sentAt`, `sentBy`, `tipsReceived`, `manualStatusTransitions`, etc. **are accepted on PUT** (no 422) and are correctly ignored / preserved by the server. Don't strip them — it's safer to leave them and let the server figure out which it owns.

### Date format gotcha

`issueDate` and `dueDate` **must be `YYYY-MM-DD`** on PUT, even though GET returns them as ISO 8601 timestamps (`2026-05-05T07:00:00.000Z`). If you pass the ISO string back unchanged you'll get HTTP 422:

```json
{
  "message": [
    "issueDate must be in YYYY-MM-DD format",
    "dueDate must be in YYYY-MM-DD format"
  ]
}
```

Strip with `.split('T')[0]` before PUT.

### invoiceId format gotcha

The path param `invoiceId` is the MongoDB internal `_id`, NOT the human-readable `invoiceNumber` (e.g. `"000197"`). Sending the number returns:

```json
{
  "statusCode": 422,
  "message": ["invoiceId must be a mongodb id"]
}
```

If your system stores invoices by `invoiceNumber` (common when receiving invoice webhooks where `customData.invoiceId` is mapped to `{{invoice.number}}`), resolve the internal `_id` first via List Invoices: `GET /invoices/?altId=...&altType=location&search={number}&limit=5`. **Apply a client-side exact-match filter on `invoice.invoiceNumber === requestedNumber`** — the `search` parameter is loose and could match `1000123` for a query of `000123`, which would silently rename the wrong invoice. See [List Invoices](#list-invoices).

### Status restrictions

**No status gating.** PUT works on a `paid` invoice — `status`, `amountPaid`, and payment history are preserved; only the fields you actually mutate change. This includes editing the bill-to address: mutate the `contactDetails.address` sub-object (`addressLine1`, `addressLine2`, `city`, `state`, `postalCode`, `countryCode`) and carry everything else over as usual (see the carry-over pattern below).

The location-scope PIT must have `invoices.write` — a contacts/conversations-only PIT will 401.

### Request Example — carry-over rename

```js
// Step 1: GET the live invoice
const getResp = await axios.get(
  `https://services.leadconnectorhq.com/invoices/${internalId}`,
  {
    params: { altId, altType: 'location' },
    headers: {
      'Authorization': `Bearer ${token}`,
      'Version': '2023-02-21',
      'Accept': 'application/json'
    }
  }
);
const invoice = getResp.data;

// Step 2: Build PUT body via carry-over
const META_FIELDS_TO_STRIP = [
  '_id', 'companyId', 'createdAt', 'updatedAt', '__v',
  'externalSources', 'lastVisitedAt', 'opportunityId', 'traceId',
];
const putBody = { ...invoice };
for (const k of META_FIELDS_TO_STRIP) delete putBody[k];
putBody.altId = altId;
putBody.altType = 'location';
putBody.name = 'New Name Here';  // the field you actually want to change
if (typeof putBody.issueDate === 'string') putBody.issueDate = putBody.issueDate.split('T')[0];
if (typeof putBody.dueDate === 'string')   putBody.dueDate   = putBody.dueDate.split('T')[0];

// Step 3: PUT
const putResp = await axios.put(
  `https://services.leadconnectorhq.com/invoices/${internalId}`,
  putBody,
  {
    headers: {
      'Authorization': `Bearer ${token}`,
      'Version': '2023-02-21',
      'Content-Type': 'application/json',
      'Accept': 'application/json'
    }
  }
);
```

### Response Example

Returns the full updated invoice object (same shape as Get Invoice). `updatedAt` is bumped, all other fields including `status`, `amountPaid`, `invoiceNumber` carried over.

### What does NOT work — the cherry-pick anti-pattern

**Don't do this:**

```js
// ❌ Will 422 if the live invoice has populated nested objects
const putBody = {
  altId, altType: 'location', name: newName,
  currency: invoice.currency,
  invoiceItems: invoice.invoiceItems,
  issueDate: invoice.issueDate.split('T')[0],
  dueDate:   invoice.dueDate.split('T')[0],
};
```

Against an invoice that has `businessDetails`, `contactDetails`, and `discount` populated, this returns 422 with all three fields flagged "should not be empty". The 7-required-fields list from the docs is necessary but not sufficient. Always spread the GET response.

> **Note:** The carry-over pattern (GET → strip meta fields → override → date-strip → PUT) is the reliable approach. Cherry-picking only the fields you want to change tends to 422 because GHL re-validates the whole object; spreading the GET response avoids this.

---

## Delete Invoice

Permanently delete an invoice.

**Method:** `DELETE`
**URL:** `/invoices/{invoiceId}`
**Scope:** `invoices.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `invoiceId` | string | Yes | Invoice ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X DELETE 'https://services.leadconnectorhq.com/invoices/inv_6578278e?altId=loc_abc123&altType=location' \
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

## Void Invoice

Mark an invoice as voided. This action is irreversible.

**Method:** `POST`
**URL:** `/invoices/{invoiceId}/void`
**Scope:** `invoices.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `invoiceId` | string | Yes | Invoice ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/inv_6578278e/void' \
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
  "_id": "inv_00000000000000order00001",
  "status": "void",
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Send Invoice

Send an invoice to the contact via email.

**Method:** `POST`
**URL:** `/invoices/{invoiceId}/send`
**Scope:** `invoices.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `invoiceId` | string | Yes | Invoice ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/inv_6578278e/send' \
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
  "_id": "inv_00000000000000order00001",
  "status": "sent",
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Record Manual Payment

Record a payment against an invoice manually (e.g., cash, check, or off-platform payments).

**Method:** `POST`
**URL:** `/invoices/{invoiceId}/record-payment`
**Scope:** `invoices.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `invoiceId` | string | Yes | Invoice ID |

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `amount` | number | Yes | Payment amount |
| `paymentMethod` | string | No | Payment method description |
| `notes` | string | No | Payment notes |

### Request Example

```bash
curl -X POST 'https://services.leadconnectorhq.com/invoices/inv_6578278e/record-payment' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "altId": "loc_abc123",
    "altType": "location",
    "amount": 999
  }'
```

### Response Example

```json
{
  "_id": "inv_00000000000000order00001",
  "status": "partially_paid",
  "amountPaid": 999,
  "amountDue": 1251,
  "updatedAt": "2026-02-23T12:00:00.000Z"
}
```

> **Status:** 🔬 Unverified -- documented from OpenAPI spec research, not tested with live API.

---

## Update Invoice Late Fees Config

Configure late fee settings on a specific invoice.

**Method:** `PATCH`
**URL:** `/invoices/{invoiceId}/late-fees-configuration`
**Scope:** Not explicitly specified in OpenAPI (likely requires `invoices.write`)

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `invoiceId` | string | Yes | Invoice ID |

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |
| `lateFeeConfig` | object | Yes | Late fee configuration object (fields inferred -- may include `enabled`, `type`, `value`, `frequency`) |

### Request Example

```bash
curl -X PATCH 'https://services.leadconnectorhq.com/invoices/inv_6578278e/late-fees-configuration' \
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

## Update Invoice Last Visited At

Update the last visited timestamp for invoice analytics/tracking.

**Method:** `PATCH`
**URL:** `/invoices/stats/last-visited-at`
**Scope:** Not explicitly specified

### Request Body (inferred)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `altId` | string | Yes | Location ID |
| `altType` | string | Yes | `"location"` |

### Request Example

```bash
curl -X PATCH 'https://services.leadconnectorhq.com/invoices/stats/last-visited-at' \
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
