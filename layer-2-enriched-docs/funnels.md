# Funnels API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Funnels API provides read access to funnels and their pages, plus full CRUD for URL redirects. It has **7 endpoints** organized in two sub-groups: **Funnel** (3 read-only endpoints) and **Redirect** (4 CRUD endpoints). All endpoints require a location-level or agency-level Bearer token and the `Version: 2021-07-28` header.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `funnels/funnel.readonly` | Read | List funnels for a location |
| `funnels/page.readonly` | Read | List pages within a funnel |
| `funnels/pagecount.readonly` | Read | Get page count for a funnel |
| `funnels/redirect.readonly` | Read | List URL redirects |
| `funnels/redirect.write` | Read/Write | Create, update, delete URL redirects |

---

## Endpoints Summary

**Funnels**

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Fetch List of Funnels](#fetch-list-of-funnels) | GET | `funnels/funnel.readonly` | List all funnels in a location |
| [Fetch List of Funnel Pages](#fetch-list-of-funnel-pages) | GET | `funnels/page.readonly` | List pages within a funnel |
| [Fetch Count of Funnel Pages](#fetch-count-of-funnel-pages) | GET | `funnels/pagecount.readonly` | Get page count for a funnel |

**Redirects**

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Create Redirect](#create-redirect) | POST | `funnels/redirect.write` | Create a new URL redirect |
| [Update Redirect By Id](#update-redirect-by-id) | PATCH | `funnels/redirect.write` | Update an existing redirect |
| [Delete Redirect By Id](#delete-redirect-by-id) | DELETE | `funnels/redirect.write` | Delete a redirect |
| [Fetch List of Redirects](#fetch-list-of-redirects) | GET | `funnels/redirect.readonly` | List all redirects |

---

## Authentication

All requests require:

```
Authorization: Bearer {access_token}
Version: 2021-07-28
```

---

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

---

## Object Schemas

### Funnel Object

```json
{
  "id": "funnel_abc123",
  "name": "Sales Funnel",
  "locationId": "ve9EPM428h8vShlRW1KT",
  "steps": [],
  "url": "/sales-funnel",
  "updatedAt": "2025-06-15T10:30:00.000Z",
  "createdAt": "2025-01-10T08:00:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique funnel identifier |
| `name` | string | Funnel name |
| `locationId` | string | Location/sub-account ID |
| `steps` | array | Array of funnel step objects |
| `url` | string | URL slug of the funnel |
| `updatedAt` | string | ISO 8601 last-update timestamp |
| `createdAt` | string | ISO 8601 creation timestamp |

### Funnel Page Object

```json
{
  "id": "page_def456",
  "name": "Landing Page",
  "funnelId": "funnel_abc123",
  "locationId": "ve9EPM428h8vShlRW1KT",
  "url": "/landing",
  "updatedAt": "2025-06-15T10:30:00.000Z",
  "createdAt": "2025-01-10T08:00:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique page identifier |
| `name` | string | Page name |
| `funnelId` | string | Parent funnel ID |
| `locationId` | string | Location ID |
| `url` | string | Page URL/slug |
| `updatedAt` | string | ISO 8601 timestamp |
| `createdAt` | string | ISO 8601 timestamp |

### Redirect Object

```json
{
  "id": "redirect_ghi789",
  "locationId": "ve9EPM428h8vShlRW1KT",
  "domain": "www.example.com",
  "path": "/old-page",
  "target": "https://example.com/new-page",
  "action": "url",
  "createdAt": "2025-06-15T10:30:00.000Z",
  "updatedAt": "2025-06-15T10:30:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique redirect identifier |
| `locationId` | string | Location ID |
| `domain` | string | Source domain for the redirect |
| `path` | string | Source URL path |
| `target` | string | Destination URL |
| `action` | string | Redirect type (e.g., `"funnel"`, `"website"`, `"url"`, `"all"`, `"proxy"`) |
| `createdAt` | string | ISO 8601 timestamp |
| `updatedAt` | string | ISO 8601 timestamp |

---

## Fetch List of Funnels

Retrieves all funnels for a location.

**Method:** `GET`
**URL:** `/funnels/funnel/list`
**Scope:** `funnels/funnel.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `limit` | integer | No | Results per page |
| `offset` | integer | No | Number of results to skip |
| `name` | string | No | Search by funnel name |
| `category` | string | No | Filter by category |
| `parentId` | string | No | Filter by parent ID |
| `type` | string | No | Filter by type |
| `search` | string | No | General search query |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/funnels/funnel/list?locationId=ve9EPM428h8vShlRW1KT&limit=10&offset=0' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "funnels": [
    {
      "id": "funnel_abc123",
      "name": "Sales Funnel",
      "locationId": "ve9EPM428h8vShlRW1KT",
      "steps": [],
      "url": "/sales-funnel",
      "updatedAt": "2025-06-15T10:30:00.000Z",
      "createdAt": "2025-01-10T08:00:00.000Z"
    }
  ],
  "count": 1,
  "total": 1
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `funnels` | array | Array of funnel objects |
| `count` | number | Number of results in this response |
| `total` | number | Total number of funnels |

### Error Codes

| Code | Description |
|------|-------------|
| 400 | Bad Request — missing locationId |
| 401 | Unauthorized |
| 422 | Unprocessable Entity |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Fetch List of Funnel Pages

Retrieves pages within a specific funnel.

**Method:** `GET`
**URL:** `/funnels/page`
**Scope:** `funnels/page.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `funnelId` | string | Yes | ID of the funnel |
| `limit` | integer | No | Results per page |
| `offset` | integer | No | Results to skip |
| `name` | string | No | Search by page name |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/funnels/page?locationId=ve9EPM428h8vShlRW1KT&funnelId=funnel_abc123&limit=10' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "pages": [
    {
      "id": "page_def456",
      "name": "Landing Page",
      "funnelId": "funnel_abc123",
      "locationId": "ve9EPM428h8vShlRW1KT",
      "url": "/landing",
      "updatedAt": "2025-06-15T10:30:00.000Z",
      "createdAt": "2025-01-10T08:00:00.000Z"
    }
  ],
  "count": 1,
  "total": 1
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `pages` | array | Array of funnel page objects |
| `count` | number | Number of results in this response |
| `total` | number | Total number of pages in the funnel |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Fetch Count of Funnel Pages

Returns the count of pages in a funnel.

**Method:** `GET`
**URL:** `/funnels/page/count`
**Scope:** `funnels/pagecount.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `funnelId` | string | Yes | ID of the funnel |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/funnels/page/count?locationId=ve9EPM428h8vShlRW1KT&funnelId=funnel_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "count": 5
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `count` | number | Total number of pages in the funnel |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Create Redirect

Creates a new URL redirect.

**Method:** `POST`
**URL:** `/funnels/lookup/redirect`
**Scope:** `funnels/redirect.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `domain` | string | Yes | Source domain (must be added to the sub-account) |
| `path` | string | Yes | Source URL path to redirect from |
| `target` | string | Yes | Destination URL to redirect to |
| `action` | string | Yes | Redirect type — e.g., `"funnel"`, `"website"`, `"url"`, `"all"`, `"proxy"` |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/funnels/lookup/redirect' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "ve9EPM428h8vShlRW1KT",
    "domain": "www.example.com",
    "path": "/old-page",
    "target": "https://example.com/new-page",
    "action": "url"
  }'
```

### Response Example

```json
{
  "id": "redirect_ghi789",
  "locationId": "ve9EPM428h8vShlRW1KT",
  "domain": "www.example.com",
  "path": "/old-page",
  "target": "https://example.com/new-page",
  "action": "url",
  "createdAt": "2025-06-15T10:30:00.000Z",
  "updatedAt": "2025-06-15T10:30:00.000Z"
}
```

### Error Codes

| Code | Description |
|------|-------------|
| 401 | Unauthorized |
| 422 | Unprocessable Entity — validation errors |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Update Redirect By Id

Updates an existing URL redirect.

**Method:** `PATCH`
**URL:** `/funnels/lookup/redirect/{id}`
**Scope:** `funnels/redirect.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Redirect ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `domain` | string | No | Updated source domain |
| `path` | string | No | Updated source path |
| `target` | string | No | Updated destination URL |
| `action` | string | No | Updated redirect type |

### Request Example

```bash
curl -X PATCH \
  'https://services.leadconnectorhq.com/funnels/lookup/redirect/redirect_ghi789' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "ve9EPM428h8vShlRW1KT",
    "target": "https://example.com/updated-page",
    "action": "url"
  }'
```

### Response Example

```json
{
  "id": "redirect_ghi789",
  "locationId": "ve9EPM428h8vShlRW1KT",
  "domain": "www.example.com",
  "path": "/old-page",
  "target": "https://example.com/updated-page",
  "action": "url",
  "createdAt": "2025-06-15T10:30:00.000Z",
  "updatedAt": "2025-06-20T14:00:00.000Z"
}
```

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Delete Redirect By Id

Deletes a URL redirect by its ID.

**Method:** `DELETE`
**URL:** `/funnels/lookup/redirect/{id}`
**Scope:** `funnels/redirect.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | Redirect ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/funnels/lookup/redirect/redirect_ghi789?locationId=ve9EPM428h8vShlRW1KT' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "succeeded": true
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `succeeded` | boolean | Whether the delete was successful |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Fetch List of Redirects

Retrieves all URL redirects for a location.

**Method:** `GET`
**URL:** `/funnels/lookup/redirect/list`
**Scope:** `funnels/redirect.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `limit` | integer | No | Results per page |
| `offset` | integer | No | Results to skip |
| `search` | string | No | Filter/search term |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/funnels/lookup/redirect/list?locationId=ve9EPM428h8vShlRW1KT&limit=20' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "redirects": [
    {
      "id": "redirect_ghi789",
      "locationId": "ve9EPM428h8vShlRW1KT",
      "domain": "www.example.com",
      "path": "/old-page",
      "target": "https://example.com/new-page",
      "action": "url",
      "createdAt": "2025-06-15T10:30:00.000Z",
      "updatedAt": "2025-06-15T10:30:00.000Z"
    }
  ],
  "count": 1,
  "total": 1
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `redirects` | array | Array of redirect objects |
| `count` | number | Number of results in this response |
| `total` | number | Total number of redirects |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 400 | Bad Request — invalid parameters |
| 401 | Unauthorized — invalid/missing token or insufficient scopes |
| 422 | Unprocessable Entity — validation failure |

---

## Notes

- All Funnel endpoints (list funnels, list pages, count pages) are **read-only**. There are no API endpoints to create, update, or delete funnels or funnel pages.
- The Redirect sub-group provides full CRUD operations for URL redirects associated with a location.
- The `action` field on redirects accepts values: `"funnel"`, `"website"`, `"url"`, `"all"`, `"proxy"`. The exact behavior of each action type depends on the GHL platform's routing logic.
- Pagination on list endpoints uses `limit` and `offset` query parameters.
- The `domain` field on Create Redirect must reference a domain already added to the sub-account in GHL.

---

> **Agent Note:** Documentation created from OpenAPI spec research. All endpoints are 🔬 unverified until tested against a live GHL API. If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) -- including its Known Issues Log. Add a `> **Tested:** {date}` note to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.
