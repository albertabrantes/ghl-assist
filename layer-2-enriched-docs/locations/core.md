# Locations (Sub-Accounts) API — Core

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Locations API — now officially called the **Sub-Account API** — enables programmatic management of sub-accounts within a GoHighLevel agency. An agency can contain hundreds of sub-accounts, each representing a client business. The API covers creation, retrieval, updating, and deletion of sub-accounts plus management of their associated resources: custom fields, custom values, tags, templates, tasks, recurring tasks, and timezones.

The API is organized into 9 sub-groups:
- **Search** — Search/list locations across an agency
- **Location Core** — Get, create, update, delete locations
- **Custom Fields** — CRUD + file upload for location-level custom fields ([custom-fields.md](./custom-fields.md))
- **Custom Values** — CRUD for location-level custom values ([custom-values.md](./custom-values.md))
- **Tags** — CRUD for location tags ([tags-templates.md](./tags-templates.md))
- **Templates** — List and delete email/SMS templates ([tags-templates.md](./tags-templates.md))
- **Tasks Search** — Search/filter tasks within a location ([tasks.md](./tasks.md))
- **Recurring Tasks** — CRUD for recurring task definitions ([tasks.md](./tasks.md))
- **Timezone** — Fetch available timezones ([tasks.md](./tasks.md))

### Agency vs. Sub-Account Access

Endpoints that operate *across* locations (Search, Create, Delete) require an **Agency-level** access token. Endpoints that operate *within* a single location (Custom Fields, Tags, Templates, Tasks, etc.) accept a **Sub-Account-level** token. Get Location and Update Location accept either token type.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `locations.readonly` | Read | View locations, search locations, fetch timezones |
| `locations.write` | Read/Write | Create, update locations (Agency Pro $497 plan only) |
| `locations.internal-access-only` | Write | Delete locations (restricted — see notes) |
| `locations/customFields.readonly` | Read | View location-level custom fields |
| `locations/customFields.write` | Read/Write | Create, update, delete custom fields; upload files |
| `locations/customValues.readonly` | Read | View location-level custom values |
| `locations/customValues.write` | Read/Write | Create, update, delete custom values |
| `locations/tags.readonly` | Read | View location tags |
| `locations/tags.write` | Read/Write | Create, update, delete tags |
| `locations/templates.readonly` | Read | View and delete templates (no write scope exists) |
| `locations/tasks.readonly` | Read | Search tasks, view recurring tasks |
| `locations/tasks.write` | Read/Write | Create, update, delete recurring tasks |

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

## Location Object Schema

### Full Location Object

```json
{
  "id": "ve9EPM428h8vShlRW1KT",
  "companyId": "5DP4iH6HLkQsiKESj6rh",
  "name": "My Business Location",
  "domain": "app.mybusiness.com",
  "email": "info@mybusiness.com",
  "phone": "+15551234567",
  "address": "123 Main St",
  "city": "Dallas",
  "state": "TX",
  "country": "US",
  "postalCode": "75001",
  "website": "https://mybusiness.com",
  "timezone": "America/Chicago",
  "firstName": "John",
  "lastName": "Doe",
  "logoUrl": "https://storage.googleapis.com/...",
  "faviconUrl": "https://...",
  "settings": {
    "allowDuplicateContact": false,
    "allowDuplicateOpportunity": false,
    "allowFacebookNameMerge": true,
    "disableContactTimezone": false
  },
  "social": {
    "facebookUrl": "https://facebook.com/mybusiness",
    "googlePlus": "",
    "linkedIn": "https://linkedin.com/company/mybusiness",
    "foursquare": "",
    "twitter": "https://twitter.com/mybusiness",
    "yelp": "",
    "instagram": "https://instagram.com/mybusiness",
    "youtube": "",
    "pinterest": "",
    "blogRss": "",
    "googlePlacesId": "ChIJxxxxx"
  },
  "business": {
    "name": "My Business LLC",
    "address": "123 Main St",
    "city": "Dallas",
    "state": "TX",
    "country": "US",
    "postalCode": "75001",
    "website": "https://mybusiness.com",
    "timezone": "America/Chicago",
    "phone": "+15551234567",
    "email": "legal@mybusiness.com",
    "logoUrl": "https://..."
  },
  "reseller": {},
  "createdAt": "2023-01-15T10:30:00.000Z",
  "updatedAt": "2024-06-20T15:45:00.000Z"
}
```

### Location Field Descriptions

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique location/sub-account ID |
| `companyId` | string | Parent agency/company ID |
| `name` | string | Location display name |
| `email` | string | Location email address |
| `phone` | string | Phone number |
| `address` | string | Street address |
| `city` | string | City |
| `state` | string | State/Province |
| `country` | string | ISO 3166-1 alpha-2 country code (e.g., `US`, `CA`, `GB`) |
| `postalCode` | string | Postal/ZIP code |
| `website` | string | Website URL |
| `timezone` | string | IANA timezone identifier (e.g., `America/Chicago`) |
| `firstName` | string | Owner/prospect first name |
| `lastName` | string | Owner/prospect last name |
| `logoUrl` | string | Logo image URL |
| `faviconUrl` | string | Favicon URL |
| `domain` | string | Custom branded domain |
| `settings` | object | Location settings (see below) |
| `social` | object | Social media URLs (see below) |
| `business` | object | Business details (see below) |
| `reseller` | object | White-label/reseller info |
| `createdAt` | string | ISO 8601 creation timestamp |
| `updatedAt` | string | ISO 8601 last modification timestamp |

### `settings` Nested Object

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `allowDuplicateContact` | boolean | `false` | Allow duplicate contacts |
| `allowDuplicateOpportunity` | boolean | `false` | Allow duplicate opportunities |
| `allowFacebookNameMerge` | boolean | `false` | Allow Facebook name merging |
| `disableContactTimezone` | boolean | `false` | Disable contact-level timezone override |

### `social` Nested Object

| Field | Type | Description |
|-------|------|-------------|
| `facebookUrl` | string | Facebook page URL |
| `googlePlus` | string | Google Business URL |
| `linkedIn` | string | LinkedIn company URL |
| `foursquare` | string | Foursquare URL |
| `twitter` | string | Twitter/X URL |
| `yelp` | string | Yelp page URL |
| `instagram` | string | Instagram URL |
| `youtube` | string | YouTube channel URL |
| `pinterest` | string | Pinterest URL |
| `blogRss` | string | Blog RSS feed URL |
| `googlePlacesId` | string | Google Places ID |

### `business` Nested Object

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Legal/DBA business name |
| `address` | string | Business street address |
| `city` | string | Business city |
| `state` | string | Business state |
| `country` | string | Business country code |
| `postalCode` | string | Business postal code |
| `website` | string | Business website |
| `timezone` | string | Business timezone |
| `phone` | string | Business phone |
| `email` | string | Business email |
| `logoUrl` | string | Business logo URL |

---

## Search Locations

Search sub-accounts across an agency. Requires an **Agency-level** token.

**Method:** `GET`
**URL:** `/locations/search`
**Scope:** `locations.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `companyId` | string | Yes | The agency/company ID |
| `skip` | integer | No | Records to skip for pagination. Default: `0` |
| `limit` | integer | No | Records to return. Default: `10`, Max: `100` |
| `order` | string | No | Sort order: `asc` or `desc` |
| `email` | string | No | Filter by location email |
| `query` | string | No | Search string to filter by name |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/search?companyId=5DP4iH6HLkQsiKESj6rh&skip=0&limit=10&order=asc' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "locations": [
    {
      "id": "ve9EPM428h8vShlRW1KT",
      "companyId": "5DP4iH6HLkQsiKESj6rh",
      "name": "My Business Location",
      "email": "info@mybusiness.com",
      "phone": "+15551234567",
      "address": "123 Main St",
      "city": "Dallas",
      "state": "TX",
      "country": "US",
      "postalCode": "75001",
      "website": "https://mybusiness.com",
      "timezone": "America/Chicago",
      "logoUrl": "https://storage.googleapis.com/...",
      "firstName": "John",
      "lastName": "Doe"
    }
  ],
  "count": 1,
  "traceId": "abc123-trace-id"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `locations` | array | Array of abbreviated location objects |
| `count` | number | Total matching results for pagination |
| `traceId` | string | Request trace identifier |

### Important Notes

- The `locations` array contains **abbreviated** location objects — the `settings`, `social`, and `business` nested objects are not included in search results. Use [Get Location](#get-location) for full details.
- The `count` field indicates total matching results, not just the current page.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Get Location

Retrieve full details of a single sub-account.

**Method:** `GET`
**URL:** `/locations/{locationId}`
**Scope:** `locations.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "location": {
    "id": "ve9EPM428h8vShlRW1KT",
    "companyId": "5DP4iH6HLkQsiKESj6rh",
    "name": "My Business Location",
    "domain": "app.mybusiness.com",
    "email": "info@mybusiness.com",
    "phone": "+15551234567",
    "address": "123 Main St",
    "city": "Dallas",
    "state": "TX",
    "country": "US",
    "postalCode": "75001",
    "website": "https://mybusiness.com",
    "timezone": "America/Chicago",
    "firstName": "John",
    "lastName": "Doe",
    "logoUrl": "https://storage.googleapis.com/...",
    "faviconUrl": "https://...",
    "settings": {
      "allowDuplicateContact": false,
      "allowDuplicateOpportunity": false,
      "allowFacebookNameMerge": true,
      "disableContactTimezone": false
    },
    "social": {
      "facebookUrl": "https://facebook.com/mybusiness",
      "googlePlus": "",
      "linkedIn": "https://linkedin.com/company/mybusiness",
      "foursquare": "",
      "twitter": "https://twitter.com/mybusiness",
      "yelp": "",
      "instagram": "https://instagram.com/mybusiness",
      "youtube": "",
      "pinterest": "",
      "blogRss": "",
      "googlePlacesId": "ChIJxxxxx"
    },
    "business": {
      "name": "My Business LLC",
      "address": "123 Main St",
      "city": "Dallas",
      "state": "TX",
      "country": "US",
      "postalCode": "75001",
      "website": "https://mybusiness.com",
      "timezone": "America/Chicago",
      "phone": "+15551234567",
      "email": "legal@mybusiness.com",
      "logoUrl": "https://..."
    },
    "reseller": {},
    "createdAt": "2023-01-15T10:30:00.000Z",
    "updatedAt": "2024-06-20T15:45:00.000Z"
  },
  "traceId": "abc123-trace-id"
}
```

### Important Notes

- The response wraps the location object inside a `"location"` key.
- The `twilio` and `mailgun` configurations are **not** returned in GET responses for security reasons.
- Accepts both Agency and Sub-Account tokens.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Create Location

Create a new sub-account under an agency. **Only available on Agency Pro ($497) plan.**

**Method:** `POST`
**URL:** `/locations/`
**Scope:** `locations.write`
**Auth:** Agency-level token required

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Location name |
| `companyId` | string | Yes | Parent agency/company ID |
| `email` | string | No | Location email |
| `phone` | string | No | Phone (E.164 format recommended) |
| `address` | string | No | Street address |
| `city` | string | No | City |
| `state` | string | No | State/Province |
| `country` | string | No | ISO 3166-1 alpha-2 country code |
| `postalCode` | string | No | Postal/ZIP code |
| `website` | string | No | Website URL |
| `timezone` | string | No | IANA timezone identifier |
| `firstName` | string | No | Owner first name |
| `lastName` | string | No | Owner last name |
| `logoUrl` | string | No | Logo URL |
| `snapshotId` | string | No | Snapshot ID to preload assets |
| `prospectInfo` | object | No | Prospect/owner info (see below) |
| `settings` | object | No | Location settings (see Location Object Schema) |
| `social` | object | No | Social media URLs (see Location Object Schema) |
| `business` | object | No | Business details (see Location Object Schema) |
| `twilio` | object | No | `{sid: string, authToken: string}` |
| `mailgun` | object | No | `{apiKey: string, domain: string}` |

The `prospectInfo` object: `{firstName: string, lastName: string, email: string, phone: string}` — all optional.

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "My Dental Practice",
    "companyId": "5DP4iH6HLkQsiKESj6rh",
    "email": "info@mydental.com",
    "phone": "+15551234567",
    "address": "123 Main St",
    "city": "Dallas",
    "state": "TX",
    "country": "US",
    "postalCode": "75001",
    "website": "https://mydental.com",
    "timezone": "America/Chicago",
    "settings": {
      "allowDuplicateContact": false,
      "allowDuplicateOpportunity": false
    }
  }'
```

### Response Example

```json
{
  "id": "newLocationId123",
  "companyId": "5DP4iH6HLkQsiKESj6rh",
  "name": "My Dental Practice",
  "email": "info@mydental.com",
  "phone": "+15551234567",
  "address": "123 Main St",
  "city": "Dallas",
  "state": "TX",
  "country": "US",
  "postalCode": "75001",
  "website": "https://mydental.com",
  "timezone": "America/Chicago",
  "traceId": "abc123-trace-id"
}
```

### Important Notes

- The `snapshotId` parameter loads a pre-built set of assets (funnels, workflows, email templates, etc.) into the new location.
- When `twilio` or `mailgun` objects are supplied, the location is configured with those messaging integrations automatically.
- The `locations.write` scope is only available on the **Agency Pro ($497/month)** plan.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Update Location

Update fields on an existing sub-account.

**Method:** `PUT`
**URL:** `/locations/{locationId}`
**Scope:** `locations.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID to update |

### Request Body

Same fields as [Create Location](#create-location) **except** `companyId` and `snapshotId` are not accepted. All fields are optional — include only the fields you want to change.

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Updated Business Name",
    "phone": "+15559876543",
    "settings": {
      "allowDuplicateContact": true
    }
  }'
```

### Response Example

```json
{
  "location": {
    "id": "ve9EPM428h8vShlRW1KT",
    "companyId": "5DP4iH6HLkQsiKESj6rh",
    "name": "Updated Business Name",
    "phone": "+15559876543",
    "settings": {
      "allowDuplicateContact": true,
      "allowDuplicateOpportunity": false,
      "allowFacebookNameMerge": true,
      "disableContactTimezone": false
    }
  },
  "traceId": "abc123-trace-id"
}
```

### Important Notes

- Accepts both Agency and Sub-Account level tokens.
- Nested objects (`settings`, `social`, `business`) are partially updated — only specified sub-fields are changed.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Delete Location

Permanently delete a sub-account and all its associated data.

**Method:** `DELETE`
**URL:** `/locations/{locationId}`
**Scope:** `locations.internal-access-only` (restricted — see notes)
**Auth:** Agency-level token required

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID to delete |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `deleteTwilioAccount` | boolean | No | Also delete the linked Twilio sub-account. Default: `false` |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT?deleteTwilioAccount=true' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "success": true,
  "message": "Location deleted successfully",
  "traceId": "abc123-trace-id"
}
```

### Important Notes

- **This action is irreversible.** All contacts, conversations, workflows, funnels, and other data within the location are permanently destroyed.
- The `locations.internal-access-only` scope in the OpenAPI spec suggests this endpoint may have restricted availability. Some marketplace apps may need `locations.write` instead. Verify with HighLevel developer support if your app requires delete capability.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.
