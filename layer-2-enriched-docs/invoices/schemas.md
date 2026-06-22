# Invoices API — Overview & Schemas

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Invoices API encompasses everything related to invoicing, estimates, recurring billing schedules, templates, and Text2Pay functionality within GoHighLevel. It contains **42 confirmed endpoints** organized across **5 sub-groups**, all living under the `/invoices/` path prefix.

The API supports full invoice lifecycle management: creating invoices, sending them to contacts, recording payments, voiding invoices, and managing recurring billing schedules. Estimates (quotes/proposals) share a nearly identical structure to invoices and can be converted into invoices upon approval. Text2Pay enables sending payment links via SMS.

**Schema completeness note:** The official GHL documentation site renders request/response schemas dynamically via JavaScript (Docusaurus SPA), making full field-level schemas inaccessible through static fetches. The complete schemas reside in the raw OpenAPI spec files (`apps/invoices.json`) on GitHub. Where request body fields could not be confirmed from official sources, they are noted as **inferred**.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `invoices.readonly` | Read | View invoices, generate numbers, get settings |
| `invoices.write` | Read/Write | Create, update, delete, void, send invoices; record payments; Text2Pay |
| `invoices/template.readonly` | Read | View invoice templates |
| `invoices/template.write` | Read/Write | Create, update, delete invoice templates |
| `invoices/schedule.readonly` | Read | View invoice schedules |
| `invoices/schedule.write` | Read/Write | Create, update, delete, manage schedules |
| `invoices/estimate.readonly` | Read | View estimates and estimate templates |
| `invoices/estimate.write` | Read/Write | Create, update, delete, send estimates; create invoice from estimate |

---

## Authentication

All requests require authentication via one of:

**OAuth 2.0 Bearer Token:**
```
Authorization: Bearer {access_token}
```

**Private Integration Token:**
```
Authorization: Bearer {private_token}
```

Both token types require the `Version: 2021-07-28` header on every request.

---

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

Both limits are scoped per marketplace app per resource (location or company).

---

## Object Schemas

### Invoice Object

Confirmed from official InvoicePaid webhook payload documentation.

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

### Invoice Fields

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Unique invoice identifier |
| `status` | string | Invoice status: `draft`, `sent`, `viewed`, `paid`, `partially_paid`, `overdue`, `void` |
| `liveMode` | boolean | Whether the payment is in live mode (vs. test mode) |
| `amountPaid` | number | Total amount already paid (in currency units) |
| `altId` | string | Location/sub-account ID |
| `altType` | string | Always `"location"` for sub-account context |
| `name` | string | Invoice name/label |
| `businessDetails` | object | Sender business information (see below) |
| `invoiceNumber` | string | Sequential invoice number (e.g., `"19"`) |
| `currency` | string | ISO 4217 currency code (e.g., `"USD"`) |
| `contactDetails` | object | Recipient contact information (see below) |
| `issueDate` | string | Issue date in `YYYY-MM-DD` format |
| `dueDate` | string | Due date in `YYYY-MM-DD` format |
| `discount` | object | Discount configuration: `{ "type": "percentage"\|"fixed", "value": number }` |
| `invoiceItems` | array | Array of line item objects (see below) |
| `total` | number | Total invoice amount |
| `title` | string | Display title (e.g., `"INVOICE"`) |
| `amountDue` | number | Remaining amount due |
| `createdAt` | string | ISO 8601 creation timestamp |
| `updatedAt` | string | ISO 8601 last update timestamp |
| `totalSummary` | object | Summary: `{ "subTotal": number, "discount": number }` |

### businessDetails Object

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Business name |
| `address` | string | Business address (single-line) |
| `phoneNo` | string | Business phone number |
| `website` | string | Business website URL |
| `logoUrl` | string | URL to business logo image |
| `customValues` | array[string] | Custom business field values |

### contactDetails Object

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | GHL Contact ID |
| `name` | string | Contact full name |
| `email` | string | Primary email address |
| `phoneNo` | string | Phone number |
| `companyName` | string | Contact company name |
| `address` | object | Address object (see below) |
| `additionalEmails` | array[object] | Array of `{ "email": string }` objects |
| `customFields` | array[string] | Custom contact field values |

### contactDetails.address Object

| Field | Type | Description |
|-------|------|-------------|
| `countryCode` | string | ISO country code (e.g., `"US"`) |
| `addressLine1` | string | Primary address line |
| `addressLine2` | string | Secondary address line |
| `city` | string | City |
| `state` | string | State/province |
| `postalCode` | string | ZIP/postal code |

### invoiceItems Array Item

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Line item ID |
| `productId` | string | Reference to GHL Products API product ID |
| `priceId` | string | Reference to GHL Products API price ID |
| `name` | string | Item name/description |
| `currency` | string | ISO currency code |
| `qty` | number | Quantity |
| `amount` | number | Unit price |
| `taxes` | array | Array of tax configuration objects |

### Schedule Configuration (inferred from UI documentation)

| Field | Type | Description |
|-------|------|-------------|
| `frequency` | string | `weekly`, `monthly`, `quarterly`, `yearly`, `custom` |
| `startDate` | string | Date to begin sending |
| `endCondition` | string | End by cycle count or specific date |
| `autoPayment` | boolean | Whether to auto-charge the contact |

### Schedule Status Values

| Status | Description |
|--------|-------------|
| `draft` | Created but not activated |
| `active` | Currently generating invoices |
| `scheduled` | Set to begin at a future date |
| `canceled` | Stopped, no further invoices |
| `completed` | All cycles finished |

---

## Webhook Events

| Event | Description |
|-------|-------------|
| `InvoiceCreate` | Fired when an invoice is created |
| `InvoiceUpdate` | Fired when an invoice is updated |
| `InvoiceDelete` | Fired when an invoice is deleted |
| `InvoiceSent` | Fired when an invoice is sent to the contact |
| `InvoicePaid` | Fired when an invoice is fully paid |
| `InvoicePartiallyPaid` | Fired when partial payment is recorded |
| `InvoiceVoid` | Fired when an invoice is voided |

All webhook payloads contain the full Invoice object schema documented above.

---

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 400 | Bad Request -- invalid parameters |
| 401 | Unauthorized -- invalid/missing token or insufficient scopes |
| 422 | Unprocessable Entity -- validation failure |

---

## General Notes

- All endpoints live under the `/invoices/` path prefix, including estimates, schedules, and templates.
- The `altId` and `altType` parameters are required on virtually every request -- `altId` is the location/sub-account ID and `altType` is always `"location"`.
- A `GET /invoices/settings` (Get Invoice Settings) endpoint also appears in the docs sidebar but is not reflected in the community-synced OpenAPI spec. It may be a newer addition; its scope is likely `invoices.readonly`.
- Estimate endpoints mirror invoice endpoints closely in structure and field naming.
- Schedule endpoints follow a lifecycle pattern: Create (draft) -> Activate -> Active -> Cancel/Complete.
- The `PATCH` endpoints for late fees and payment methods configuration do not have explicitly documented scopes in the OpenAPI spec; they likely require the corresponding write scope.
- Some request body fields are marked as **inferred** -- these were reconstructed from response schemas, webhook payloads, and API patterns but have not been confirmed from the raw OpenAPI spec files.

---

> **Agent Note:** Documentation created from OpenAPI spec research. All endpoints are 🔬 unverified until tested against a live GHL API. If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../../index.md) -- including its Known Issues Log. Add a `> **Tested:**` note to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.
