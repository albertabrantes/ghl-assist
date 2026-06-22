# Opportunities API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Opportunities API allows you to manage sales opportunities within pipelines. Opportunities track potential deals through various stages until they are won or lost.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `opportunities.readonly` | Read | View opportunities and pipelines |
| `opportunities.write` | Read/Write | Create, update, delete opportunities |

---

## Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get Pipelines](#get-pipelines) | GET | readonly | List all pipelines for a location |
| [Search Opportunities](#search-opportunities) | GET | readonly | Search opportunities with filters |
| [Search Opportunities (Advanced)](#search-opportunities-advanced) | POST | readonly | Complex filtering with logical operators |
| [Get Opportunity](#get-opportunity) | GET | readonly | Get a single opportunity by ID |
| [Create Opportunity](#create-opportunity) | POST | write | Create a new opportunity |
| [Update Opportunity](#update-opportunity) | PUT | write | Update an existing opportunity |
| [Upsert Opportunity](#upsert-opportunity) | POST | write | Create or update in one call |
| [Update Opportunity Status](#update-opportunity-status) | PUT | write | Update only the status field |
| [Delete Opportunity](#delete-opportunity) | DELETE | write | Delete an opportunity |
| [Add Followers](#add-followers) | POST | write | Add team members as followers |
| [Remove Followers](#remove-followers) | DELETE | write | Remove followers from opportunity |

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

---

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

## Get Pipelines

Retrieves all sales pipelines configured for a location, including their stages.

**Method:** `GET`
**URL:** `/opportunities/pipelines`
**Scope:** `opportunities.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/opportunities/pipelines?locationId=abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "pipelines": [
    {
      "id": "pipeline_abc123",
      "name": "Sales Pipeline",
      "locationId": "abc123",
      "stages": [
        {
          "id": "stage_001",
          "name": "New Lead",
          "order": 0,
          "isClosed": false,
          "isWon": false
        },
        {
          "id": "stage_002",
          "name": "Qualified",
          "order": 1,
          "isClosed": false,
          "isWon": false
        },
        {
          "id": "stage_003",
          "name": "Won",
          "order": 2,
          "isClosed": true,
          "isWon": true
        },
        {
          "id": "stage_004",
          "name": "Lost",
          "order": 3,
          "isClosed": true,
          "isWon": false
        }
      ]
    }
  ]
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `pipelines` | array | List of pipeline objects |
| `pipelines[].id` | string | Unique pipeline identifier |
| `pipelines[].name` | string | Pipeline display name |
| `pipelines[].locationId` | string | Associated location ID |
| `pipelines[].stages` | array | Ordered list of stages |
| `pipelines[].stages[].id` | string | Unique stage identifier |
| `pipelines[].stages[].name` | string | Stage display name |
| `pipelines[].stages[].order` | number | Position in pipeline (0-indexed) |
| `pipelines[].stages[].isClosed` | boolean | Whether stage is a closed state |
| `pipelines[].stages[].isWon` | boolean | Whether stage represents a win |

> **Note:** Every pipeline automatically has "Won" and "Lost" stages.

---

## Search Opportunities

Search for opportunities using basic filters.

**Method:** `GET`
**URL:** `/opportunities/search`
**Scope:** `opportunities.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `location_id` | string | Yes | Location to search within |
| `q` | string | No | Search query (matches name, contact, email) |
| `pipeline_id` | string | No | Filter by pipeline ID |
| `pipeline_stage_id` | string | No | Filter by stage ID |
| `status` | string | No | Filter by status: `open`, `won`, `lost`, `abandoned` |
| `assignedTo` | string | No | Filter by assigned user ID |
| `contactId` | string | No | Filter by contact ID |
| `limit` | number | No | Results per page (max 100, default 20) |
| `startAfter` | string | No | Cursor for pagination |
| `startAfterId` | string | No | ID-based pagination |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/opportunities/search?location_id=abc123&pipeline_id=pipe_001&limit=50' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "opportunities": [
    {
      "id": "opp_12345",
      "name": "Website Redesign Project",
      "monetaryValue": 5000,
      "pipelineId": "pipe_001",
      "pipelineStageId": "stage_002",
      "status": "open",
      "contactId": "contact_789",
      "assignedTo": "user_456",
      "source": "Website Form",
      "customFields": [
        { "id": "field_abc", "fieldValueString": "some value", "type": "string" }
      ],
      "createdAt": "2026-01-15T10:30:00Z",
      "updatedAt": "2026-02-01T14:22:00Z"
    }
  ],
  "meta": {
    "total": 150,
    "nextPageUrl": "https://services.leadconnectorhq.com/opportunities/search?location_id=abc123&limit=100&startAfter=1772988520867&startAfterId=EXAMPLEOpportunity01",
    "startAfter": 1772988520867,
    "startAfterId": "EXAMPLEOpportunity01",
    "currentPage": 1,
    "nextPage": 2,
    "prevPage": null
  }
}
```

> **Tested:** Verified in production (multiple pages of results).

### Pagination

The `GET /opportunities/search` endpoint uses **cursor-based pagination**.

| Parameter | Description |
|-----------|-------------|
| `limit` | Max results per page. Maximum: `100`. Default: `20`. |
| `startAfter` | Numeric timestamp from the previous response's `meta.startAfter`. Required together with `startAfterId` for correct paging. |
| `startAfterId` | Opportunity id from the previous response's `meta.startAfterId`. Required together with `startAfter`. |

**Real meta shape (verified):** the actual response uses `nextPage` / `nextPageUrl` / `startAfter` / `startAfterId`. There is **no** `meta.hasMore` and **no** `meta.nextCursor`; do NOT rely on those fields.

**End-of-pages detection:** check `meta.nextPage === null` (or `undefined`), or that the returned `opportunities` array is shorter than `limit`.

**Pagination loop pattern:**

```javascript
let hasMore = true;
let startAfter;
let startAfterId;
const allOpportunities = [];
const PAGE_SIZE = 100;

while (hasMore) {
  const params = new URLSearchParams({
    location_id: LOCATION_ID,
    limit: String(PAGE_SIZE)
  });
  if (startAfter) params.set('startAfter', startAfter);
  if (startAfterId) params.set('startAfterId', startAfterId);

  const response = await axios.get(
    `https://services.leadconnectorhq.com/opportunities/search?${params}`,
    { headers: { Authorization: `Bearer ${TOKEN}`, Version: '2021-07-28' } }
  );

  const { opportunities, meta } = response.data;
  allOpportunities.push(...opportunities);

  hasMore =
    meta.nextPage !== null &&
    meta.nextPage !== undefined &&
    Boolean(meta.startAfter) &&
    opportunities.length === PAGE_SIZE;
  startAfter = meta.startAfter !== undefined && meta.startAfter !== null
    ? String(meta.startAfter)
    : undefined;
  startAfterId = meta.startAfterId || undefined;

  // Rate limit: 100 requests / 10 seconds
  if (hasMore) await new Promise(r => setTimeout(r, 500));
}
```

### Important Notes (Verified in Production)

1. **`customFields` ARE included** in search results — each opportunity contains its full `customFields` array, unlike some other endpoints. Each entry uses the shape `{ id, fieldValueString, type }` (or `fieldValueArray` / `fieldValueObject` depending on field type).

2. **`contactId` is always present** on every opportunity, making it safe to use for enrichment lookups.

3. **Cursor values must be passed together**: `meta.startAfter` (a numeric timestamp) and `meta.startAfterId` (the last opp id on the previous page) work together. Passing just one is unreliable.

4. **`meta.total`**: Represents the approximate total count. May not be perfectly accurate for very large result sets.

5. **`meta.currentPage` quirk**: Every paginated response returns `currentPage: 1` and `nextPage: 2` regardless of which page was actually returned. Do NOT use `currentPage` to detect progress; rely on `meta.startAfter` advancing per page.

6. **Rate limiting**: The standard burst limit (100 req/10s) applies. When paginating through large pipelines, a 500ms delay between pages is recommended to stay well within limits. Stop processing immediately on HTTP 429.

7. **`skip` is rejected**: Passing `skip` as a query param returns HTTP 422 `property skip should not exist`. The endpoint is cursor-only.

8. **Status filter quirk**: Filtering by `status=open` returns active opportunities. Opportunities moved to "won"/"lost"/"abandoned" status will not appear unless explicitly filtered for.

9. **Empty pipeline**: If a pipeline has no opportunities, the response returns `{ "opportunities": [], "meta": { "total": 0, ... } }`.

---

## Search Opportunities (Advanced)

Search opportunities using complex filter combinations with logical operators.

**Method:** `POST`
**URL:** `/opportunities/search`
**Scope:** `opportunities.readonly`

### Request Body

```json
{
  "locationId": "abc123",
  "pipelineId": "pipe_001",
  "filters": [
    {
      "field": "monetaryValue",
      "operator": "gte",
      "value": 1000
    },
    {
      "field": "status",
      "operator": "eq",
      "value": "open"
    }
  ],
  "searchQuery": "acme",
  "sort": {
    "field": "createdAt",
    "direction": "desc"
  },
  "page": 1,
  "limit": 50
}
```

### Filter Operators

| Operator | Description |
|----------|-------------|
| `eq` | Equals |
| `neq` | Not equals |
| `gt` | Greater than |
| `gte` | Greater than or equal |
| `lt` | Less than |
| `lte` | Less than or equal |
| `contains` | Contains substring |
| `startsWith` | Starts with |
| `endsWith` | Ends with |

---

## Get Opportunity

Retrieve a single opportunity by ID.

**Method:** `GET`
**URL:** `/opportunities/{id}`
**Scope:** `opportunities.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The opportunity ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/opportunities/opp_12345' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "opportunity": {
    "id": "opp_12345",
    "name": "Website Redesign Project",
    "monetaryValue": 5000,
    "pipelineId": "pipe_001",
    "pipelineStageId": "stage_002",
    "pipelineStageName": "Qualified",
    "status": "open",
    "contactId": "contact_789",
    "contact": {
      "id": "contact_789",
      "name": "John Smith",
      "email": "john@example.com",
      "phone": "+1234567890"
    },
    "assignedTo": "user_456",
    "source": "Website Form",
    "customFields": [
      {
        "id": "cf_001",
        "key": "project_type",
        "fieldValue": "Enterprise"
      }
    ],
    "followers": ["user_456", "user_789"],
    "createdAt": "2026-01-15T10:30:00Z",
    "updatedAt": "2026-02-01T14:22:00Z",
    "lastStatusChangeAt": "2026-01-20T09:00:00Z"
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique opportunity identifier |
| `name` | string | Opportunity display name |
| `monetaryValue` | number | Financial value of the deal |
| `pipelineId` | string | Associated pipeline ID |
| `pipelineStageId` | string | Current stage ID |
| `pipelineStageName` | string | Current stage name |
| `status` | string | Status: `open`, `won`, `lost`, `abandoned` |
| `contactId` | string | Associated contact ID |
| `contact` | object | Embedded contact details |
| `assignedTo` | string | Assigned user ID |
| `source` | string | Origin of the opportunity |
| `customFields` | array | Custom field values |
| `followers` | array | User IDs following this opportunity |
| `createdAt` | string | ISO 8601 creation timestamp |
| `updatedAt` | string | ISO 8601 last update timestamp |
| `lastStatusChangeAt` | string | ISO 8601 last status change |

---

## Create Opportunity

Create a new opportunity in a pipeline.

**Method:** `POST`
**URL:** `/opportunities/`
**Scope:** `opportunities.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `pipelineId` | string | Yes | Target pipeline ID |
| `pipelineStageId` | string | Yes | Initial stage ID |
| `name` | string | Yes | Opportunity name |
| `locationId` | string | No | Location ID (required for some auth types) |
| `contactId` | string | No | Associated contact ID |
| `monetaryValue` | number | No | Deal value |
| `assignedTo` | string | No | User ID to assign |
| `source` | string | No | Opportunity source |
| `status` | string | No | Initial status (default: `open`) |
| `customFields` | array | No | Custom field values |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/opportunities/' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "pipelineId": "pipe_001",
    "pipelineStageId": "stage_001",
    "name": "New Enterprise Deal",
    "contactId": "contact_789",
    "monetaryValue": 10000,
    "assignedTo": "user_456",
    "source": "Referral",
    "customFields": [
      {
        "id": "cf_001",
        "field_value": "Enterprise"
      }
    ]
  }'
```

> **Custom field write shape:** the request body uses `field_value` (snake_case), per the OpenAPI spec at `customFieldsInputStringSchema` / `customFieldsInputArraySchema` / `customFieldsInputObjectSchema`. Note this is NOT symmetric with the read shape — search responses return `fieldValueString` / `fieldValueArray` / `fieldValueObject` (camelCase). Verified.

### Response

Returns the created opportunity object with HTTP status `201 Created`.

---

## Update Opportunity

Update an existing opportunity.

**Method:** `PUT`
**URL:** `/opportunities/{id}`
**Scope:** `opportunities.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The opportunity ID |

### Request Body

All fields are optional. Only include fields you want to update.

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Opportunity name |
| `pipelineId` | string | Move to different pipeline |
| `pipelineStageId` | string | Move to different stage |
| `monetaryValue` | number | Deal value |
| `contactId` | string | Associated contact |
| `assignedTo` | string | Assigned user |
| `source` | string | Opportunity source |
| `status` | string | Status: `open`, `won`, `lost`, `abandoned` |
| `customFields` | array | Custom field values |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/opportunities/opp_12345' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "pipelineStageId": "stage_003",
    "monetaryValue": 12000,
    "status": "won"
  }'
```

**Updating a single custom field** (preserves all other custom fields on the opportunity):

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/opportunities/opp_12345' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "customFields": [
      { "id": "EXAMPLEOpportunity02", "field_value": "Offsite Location" }
    ]
  }'
```

> **Custom field updates (per spec):** the OpenAPI schema marks each entry as `{ id, field_value }` and treats `customFields` as a partial-update array — passing a subset is the documented pattern. Empirical verification that PUTing a single custom field leaves the others untouched is not yet confirmed.

### Response

Returns the updated opportunity object with HTTP status `200 OK`.

---

## Upsert Opportunity

Create a new opportunity or update an existing one. Matches on `contactId` + `name` combination.

**Method:** `POST`
**URL:** `/opportunities/upsert`
**Scope:** `opportunities.write`

### Request Body

Same as [Create Opportunity](#create-opportunity).

### Matching Logic

1. If an opportunity exists with the same `contactId` AND `name` → **Update**
2. Otherwise → **Create new**

### Response

- `201 Created` - New opportunity created
- `200 OK` - Existing opportunity updated

---

## Update Opportunity Status

Update only the status field without modifying other data.

**Method:** `PUT`
**URL:** `/opportunities/{id}/status`
**Scope:** `opportunities.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The opportunity ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `status` | string | Yes | New status: `open`, `won`, `lost`, `abandoned` |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/opportunities/opp_12345/status' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "status": "won"
  }'
```

---

## Delete Opportunity

Delete an opportunity. This is a soft delete with a 2-month recovery window.

**Method:** `DELETE`
**URL:** `/opportunities/{id}`
**Scope:** `opportunities.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The opportunity ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/opportunities/opp_12345' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with empty body or confirmation message.

---

## Add Followers

Add team members as followers to receive updates about an opportunity.

**Method:** `POST`
**URL:** `/opportunities/{id}/followers`
**Scope:** `opportunities.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The opportunity ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `followers` | array | Yes | Array of user IDs to add |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/opportunities/opp_12345/followers' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "followers": ["user_789", "user_101"]
  }'
```

---

## Remove Followers

Remove team members from following an opportunity.

**Method:** `DELETE`
**URL:** `/opportunities/{id}/followers`
**Scope:** `opportunities.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The opportunity ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `followers` | array | Yes | Array of user IDs to remove |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/opportunities/opp_12345/followers' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "followers": ["user_789"]
  }'
```

---

## Webhooks

The Opportunities API supports webhooks for real-time notifications.

### Available Events

| Event | Trigger |
|-------|---------|
| `OpportunityCreate` | New opportunity created |
| `OpportunityDelete` | Opportunity deleted |
| `OpportunityStageUpdate` | Stage changed |
| `OpportunityStatusUpdate` | Status changed |
| `OpportunityMonetaryValueUpdate` | Value changed |

### Webhook Payload Example

```json
{
  "event": "OpportunityStageUpdate",
  "locationId": "abc123",
  "opportunity": {
    "id": "opp_12345",
    "name": "Website Redesign Project",
    "pipelineId": "pipe_001",
    "pipelineStageId": "stage_003",
    "previousStageId": "stage_002",
    "status": "open"
  },
  "timestamp": "2026-02-04T10:30:00Z"
}
```

---

## Error Responses

| Status Code | Description |
|-------------|-------------|
| `400` | Bad Request - Invalid parameters |
| `401` | Unauthorized - Invalid or missing token |
| `403` | Forbidden - Insufficient scope |
| `404` | Not Found - Resource doesn't exist |
| `422` | Unprocessable Entity - Validation error |
| `429` | Too Many Requests - Rate limit exceeded |

### Error Response Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "pipelineStageId is required",
    "details": [
      {
        "field": "pipelineStageId",
        "message": "This field is required"
      }
    ]
  }
}
```

---

## References

- [HighLevel API Documentation](https://marketplace.gohighlevel.com/docs/)
- [OAuth Scopes Reference](https://marketplace.gohighlevel.com/docs/Authorization/Scopes/index.html)
- [GitHub API Docs Repository](https://github.com/GoHighLevel/highlevel-api-docs)

---

> **Agent Note:** If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:** {date}` note to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.
