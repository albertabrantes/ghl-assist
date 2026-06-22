# Contacts API — Core CRUD

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Contacts API allows you to manage contacts (leads, customers, prospects) within GoHighLevel. Contacts are the foundation of CRM operations and can be associated with opportunities, appointments, workflows, campaigns, and more.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `contacts.readonly` | Read | View contacts, notes, tasks, and appointments |
| `contacts.write` | Read/Write | Create, update, delete contacts and related data |

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

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

**Rate Limit Headers:**
- `X-RateLimit-Limit-Daily` - Total daily limit
- `X-RateLimit-Daily-Remaining` - Remaining daily requests
- `X-RateLimit-Interval-Milliseconds` - Burst interval window
- `X-RateLimit-Max` - Max requests in burst interval
- `X-RateLimit-Remaining` - Remaining burst requests

When exceeded: HTTP `429 Too Many Requests` with `Retry-After` header.

---

## Contact Object Schema

### Full Contact Object

```json
{
  "id": "contact_abc123",
  "locationId": "loc_xyz789",
  "firstName": "John",
  "lastName": "Smith",
  "name": "John Smith",
  "email": "john.smith@example.com",
  "emailLowerCase": "john.smith@example.com",
  "phone": "+1234567890",
  "phoneE164": "+1234567890",
  "address1": "123 Main Street",
  "city": "San Francisco",
  "state": "CA",
  "postalCode": "94102",
  "country": "US",
  "companyName": "Acme Corporation",
  "website": "https://example.com",
  "timezone": "America/Los_Angeles",
  "dnd": true,
  "dndSettings": {
    "Call": {
      "status": "active",
      "message": "Please do not call",
      "code": "STOP"
    },
    "Email": {
      "status": "active",
      "message": "Unsubscribed",
      "code": "UNSUBSCRIBE"
    },
    "SMS": {
      "status": "active",
      "message": "Stop SMS",
      "code": "STOP"
    },
    "WhatsApp": {
      "status": "active",
      "message": "Stop WhatsApp",
      "code": "STOP"
    },
    "GMB": {
      "status": "active",
      "message": "Stop GMB messages",
      "code": "STOP"
    },
    "FB": {
      "status": "active",
      "message": "Stop Facebook messages",
      "code": "STOP"
    }
  },
  "tags": ["lead", "qualified", "high-value"],
  "customFields": [
    {
      "id": "cf_001",
      "key": "industry",
      "field_value": "Technology"
    },
    {
      "id": "cf_002",
      "key": "budget",
      "field_value": "50000"
    }
  ],
  "source": "Website Form",
  "assignedTo": "user_456",
  "dateOfBirth": "1985-03-15",
  "ssn": "XXX-XX-1234",
  "attributions": [
    {
      "url": "https://example.com/landing-page",
      "campaign": "Summer Sale 2026",
      "utmSource": "google",
      "utmMedium": "cpc",
      "utmCampaign": "summer_sale",
      "utmContent": "ad_variant_a",
      "utmTerm": "web design",
      "fbclid": "fb_click_id_123",
      "gclid": "google_click_id_456",
      "msclkid": "bing_click_id_789",
      "dclid": "display_click_id_012"
    }
  ],
  "businessId": "business_789",
  "contactType": "lead",
  "followers": ["user_456", "user_789"],
  "createdAt": "2026-01-15T10:30:00Z",
  "updatedAt": "2026-02-04T14:22:00Z",
  "lastActivity": "2026-02-04T14:22:00Z"
}
```

### Contact Field Descriptions

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique contact identifier |
| `locationId` | string | Associated location/sub-account ID |
| `firstName` | string | Contact's first name |
| `lastName` | string | Contact's last name |
| `name` | string | Full name (auto-generated from first + last) |
| `email` | string | Primary email address |
| `emailLowerCase` | string | Lowercase version for matching |
| `phone` | string | Primary phone number |
| `phoneE164` | string | Phone in E.164 format (+country_code) |
| `address1` | string | Street address |
| `city` | string | City |
| `state` | string | State/province |
| `postalCode` | string | ZIP/postal code |
| `country` | string | Country (ISO code) |
| `companyName` | string | Associated company |
| `website` | string | Contact's website URL |
| `timezone` | string | IANA timezone (e.g., America/New_York) |
| `dnd` | boolean | Global Do Not Disturb status |
| `dndSettings` | object | Channel-specific DND settings |
| `tags` | array | Array of tag strings |
| `customFields` | array | Custom field values |
| `source` | string | Origin/source of the contact |
| `assignedTo` | string | Assigned user ID |
| `dateOfBirth` | string | Birth date (YYYY-MM-DD) |
| `ssn` | string | Social security number (masked) |
| `attributions` | array | Marketing attribution data |
| `businessId` | string | Associated business ID |
| `contactType` | string | Type: `lead`, `customer`, `prospect` |
| `followers` | array | User IDs following this contact |
| `createdAt` | string | ISO 8601 creation timestamp |
| `updatedAt` | string | ISO 8601 last update timestamp |
| `lastActivity` | string | ISO 8601 last activity timestamp |

### DND Settings Structure

The `dndSettings` object controls Do Not Disturb preferences per channel:

```json
{
  "Call": {
    "status": "active",      // "active" or "inactive"
    "message": "User opt-out message",
    "code": "STOP"          // Opt-out code used
  },
  "Email": { ... },
  "SMS": { ... },
  "WhatsApp": { ... },
  "GMB": { ... },           // Google My Business
  "FB": { ... }             // Facebook Messenger
}
```

**Available Channels:** `Call`, `Email`, `SMS`, `WhatsApp`, `GMB`, `FB`

---

## Create Contact

Create a new contact in the specified location.

**Method:** `POST`
**URL:** `/contacts/`
**Scope:** `contacts.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `firstName` | string | No | First name |
| `lastName` | string | No | Last name |
| `name` | string | No | Full name (auto-generated if first/last provided) |
| `email` | string | No | Email address |
| `phone` | string | No | Phone number |
| `address1` | string | No | Street address |
| `city` | string | No | City |
| `state` | string | No | State/province |
| `postalCode` | string | No | ZIP/postal code |
| `country` | string | No | Country code |
| `companyName` | string | No | Company name |
| `website` | string | No | Website URL |
| `timezone` | string | No | IANA timezone |
| `dnd` | boolean | No | Global DND status |
| `dndSettings` | object | No | Channel-specific DND |
| `tags` | array | No | Array of tag strings |
| `customFields` | array | No | Custom field values |
| `source` | string | No | Contact source |
| `assignedTo` | string | No | Assigned user ID |
| `dateOfBirth` | string | No | Birth date (YYYY-MM-DD) |
| `contactType` | string | No | Type: `lead`, `customer`, `prospect` |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_xyz789",
    "firstName": "Jane",
    "lastName": "Doe",
    "email": "jane.doe@example.com",
    "phone": "+14155551234",
    "companyName": "Tech Startup Inc",
    "tags": ["lead", "webinar-attendee"],
    "source": "Webinar Registration",
    "customFields": [
      {
        "id": "cf_001",
        "field_value": "Enterprise"
      }
    ]
  }'
```

### Response Example

```json
{
  "contact": {
    "id": "contact_new123",
    "locationId": "loc_xyz789",
    "firstName": "Jane",
    "lastName": "Doe",
    "name": "Jane Doe",
    "email": "jane.doe@example.com",
    "emailLowerCase": "jane.doe@example.com",
    "phone": "+14155551234",
    "phoneE164": "+14155551234",
    "companyName": "Tech Startup Inc",
    "tags": ["lead", "webinar-attendee"],
    "source": "Webinar Registration",
    "customFields": [
      {
        "id": "cf_001",
        "key": "company_size",
        "field_value": "Enterprise"
      }
    ],
    "dnd": false,
    "createdAt": "2026-02-04T15:30:00Z",
    "updatedAt": "2026-02-04T15:30:00Z"
  }
}
```

### Response

Returns the created contact object with HTTP status `201 Created`.

---

## Get Contact

Retrieve a single contact by ID with full details.

**Method:** `GET`
**URL:** `/contacts/{contactId}`
**Scope:** `contacts.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/contacts/contact_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "contact": {
    "id": "contact_abc123",
    "locationId": "loc_xyz789",
    "firstName": "John",
    "lastName": "Smith",
    "name": "John Smith",
    "email": "john.smith@example.com",
    "emailLowerCase": "john.smith@example.com",
    "phone": "+1234567890",
    "phoneE164": "+1234567890",
    "address1": "123 Main Street",
    "city": "San Francisco",
    "state": "CA",
    "postalCode": "94102",
    "country": "US",
    "companyName": "Acme Corporation",
    "timezone": "America/Los_Angeles",
    "dnd": false,
    "tags": ["customer", "vip"],
    "customFields": [
      {
        "id": "cf_001",
        "key": "industry",
        "field_value": "Technology"
      }
    ],
    "source": "Website Form",
    "assignedTo": "user_456",
    "contactType": "customer",
    "createdAt": "2026-01-15T10:30:00Z",
    "updatedAt": "2026-02-04T14:22:00Z"
  }
}
```

---

## Update Contact

Update an existing contact's information.

**Method:** `PUT`
**URL:** `/contacts/{contactId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Body

All fields are optional. Only include fields you want to update.

| Field | Type | Description |
|-------|------|-------------|
| `firstName` | string | First name |
| `lastName` | string | Last name |
| `email` | string | Email address |
| `phone` | string | Phone number |
| `address1` | string | Street address |
| `city` | string | City |
| `state` | string | State/province |
| `postalCode` | string | ZIP/postal code |
| `country` | string | Country code |
| `companyName` | string | Company name |
| `website` | string | Website URL |
| `timezone` | string | IANA timezone |
| `dnd` | boolean | Global DND status |
| `dndSettings` | object | Channel-specific DND |
| `tags` | array | Array of tag strings (replaces existing) |
| `customFields` | array | Custom field values |
| `source` | string | Contact source |
| `assignedTo` | string | Assigned user ID |
| `dateOfBirth` | string | Birth date (YYYY-MM-DD) |
| `contactType` | string | Type: `lead`, `customer`, `prospect` |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/contacts/contact_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "firstName": "John",
    "lastName": "Smith-Johnson",
    "phone": "+14155559999",
    "companyName": "Acme Corp (Updated)",
    "contactType": "customer"
  }'
```

### Response

Returns the updated contact object with HTTP status `200 OK`.

---

## Delete Contact

Delete a contact permanently.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/contact_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with confirmation:

```json
{
  "success": true,
  "message": "Contact deleted successfully"
}
```

> **Warning:** Contact deletion is permanent and cannot be undone. All associated notes, tasks, and history will also be deleted.

---

## Upsert Contact

Create a new contact or update an existing one in a single operation.

**Method:** `POST`
**URL:** `/contacts/upsert`
**Scope:** `contacts.write`

### Request Body

Same as [Create Contact](#create-contact).

### Matching Logic

The upsert operation matches contacts using the following priority:

1. If `id` is provided → **Update** that specific contact
2. Else if `email` matches existing contact → **Update**
3. Else if `phone` matches existing contact → **Update**
4. Otherwise → **Create new**

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/upsert' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_xyz789",
    "email": "john.smith@example.com",
    "firstName": "John",
    "lastName": "Smith",
    "phone": "+1234567890",
    "tags": ["customer", "updated"]
  }'
```

### Response

- `201 Created` - New contact created
- `200 OK` - Existing contact updated

---

## Get Contacts by Business

Retrieve all contacts associated with a specific business ID.

**Method:** `GET`
**URL:** `/contacts/business/{businessId}`
**Scope:** `contacts.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `businessId` | string | Yes | The business ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `limit` | number | No | Results per page (max 100, default 20) |
| `startAfter` | string | No | Pagination cursor |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/contacts/business/business_789?limit=50' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response

Returns an array of contacts with the same structure as the Search Contacts response (see [search.md](./search.md)).
