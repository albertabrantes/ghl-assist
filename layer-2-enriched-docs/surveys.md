# Surveys API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Surveys API provides **2 read-only endpoints** for accessing GHL's multi-step questionnaire tool. Surveys are built within the GHL platform and can be embedded or shared to collect structured responses. Survey submissions **auto-create contacts** when no existing match is found by email or phone. There is no write scope for surveys — the API does not support creating surveys or submitting responses programmatically.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `surveys.readonly` | Read | View surveys and their submissions |

> **Note:** There is no `surveys.write` scope. Survey creation and editing is only available through the GHL platform UI.

---

## Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get Surveys](#get-surveys) | GET | readonly | List all surveys in a location |
| [Get Surveys Submissions](#get-surveys-submissions) | GET | readonly | List survey submissions with filtering |

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

### Survey Object

```json
{
  "id": "surv_abc123def456",
  "name": "Customer Satisfaction Survey",
  "locationId": "ve9EPM428h8vShlRW1KT"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique survey identifier |
| `name` | string | Survey name |
| `locationId` | string | Location/sub-account ID |

### Survey Submission Object

```json
{
  "id": "sub_mno345pqr678",
  "contactId": "ctc_stu901vwx234",
  "createdAt": "2024-07-20T10:15:00.000Z",
  "surveyId": "surv_abc123def456",
  "name": "Jane Smith",
  "email": "jane@example.com",
  "others": {
    "satisfaction_rating": "5",
    "feedback_comments": "Great service!",
    "recommend": "Yes"
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Submission ID |
| `contactId` | string | Associated contact ID |
| `createdAt` | string | ISO 8601 timestamp |
| `surveyId` | string | ID of the submitted survey |
| `name` | string | Contact's name |
| `email` | string | Contact's email |
| `others` | object | Key-value map of survey responses |

---

## Get Surveys

List all surveys in a location.

**Method:** `GET`
**URL:** `/surveys/`
**Scope:** `surveys.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `skip` | integer | No | Records to skip (default 0) |
| `limit` | integer | No | Max results (default 20) |
| `type` | string | No | Filter by survey type |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/surveys/?locationId=ve9EPM428h8vShlRW1KT&limit=20&skip=0' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "surveys": [
    {
      "id": "surv_abc123def456",
      "name": "Customer Satisfaction Survey",
      "locationId": "ve9EPM428h8vShlRW1KT"
    },
    {
      "id": "surv_ghi789jkl012",
      "name": "Event Feedback Survey",
      "locationId": "ve9EPM428h8vShlRW1KT"
    }
  ],
  "total": 2
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `surveys` | array | Array of survey objects |
| `total` | number | Total number of surveys |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Get Surveys Submissions

List survey submissions with filtering and pagination.

**Method:** `GET`
**URL:** `/surveys/submissions`
**Scope:** `surveys.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `page` | integer | No | Page number (starts at 1) |
| `limit` | integer | No | Results per page (default 20) |
| `surveyId` | string | No | Filter by specific survey ID |
| `q` | string | No | Search query |
| `startAt` | string | No | Start date (YYYY-MM-DD) |
| `endAt` | string | No | End date (YYYY-MM-DD) |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/surveys/submissions?locationId=ve9EPM428h8vShlRW1KT&page=1&limit=20&surveyId=surv_abc123def456&startAt=2024-01-01&endAt=2024-12-31' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "submissions": [
    {
      "id": "sub_mno345pqr678",
      "contactId": "ctc_stu901vwx234",
      "createdAt": "2024-07-20T10:15:00.000Z",
      "surveyId": "surv_abc123def456",
      "name": "Jane Smith",
      "email": "jane@example.com",
      "others": {
        "satisfaction_rating": "5",
        "feedback_comments": "Great service!",
        "recommend": "Yes"
      }
    }
  ],
  "meta": {
    "total": 85,
    "currentPage": 1,
    "nextPage": 2,
    "prevPage": null
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `submissions` | array | Array of survey submission objects |
| `meta` | object | Pagination metadata |
| `meta.total` | number | Total number of submissions matching the query |
| `meta.currentPage` | number | Current page number |
| `meta.nextPage` | number/null | Next page number, or null if on the last page |
| `meta.prevPage` | number/null | Previous page number, or null if on the first page |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 400 | Bad Request — invalid parameters |
| 401 | Unauthorized — invalid/missing token or insufficient scopes |
| 422 | Unprocessable Entity — validation failure |

---

## Notes

- **Contact auto-creation:** Survey submissions auto-create contacts if no match exists by email or phone. Every submission includes a `contactId` linking to the created or matched contact record.
- **No write API:** There is no public API to create surveys or submit responses programmatically. Survey building and submission are only available through the GHL platform UI or embedded survey links.
- **Pagination inconsistency:** The listing endpoint (Get Surveys) uses **skip/limit** pagination, while the submissions endpoint uses **page/limit** pagination. These are different pagination models — do not mix them.
- **The `others` field:** Custom survey responses are stored as a flat key-value object in the `others` field. Keys correspond to field IDs or names as configured in the survey builder.
- **No programmatic submission:** Some developers have found undocumented internal endpoints for submitting surveys, but these are unsupported and may break without notice.

---

> **Agent Note:** Endpoints marked 🔬 are documented from OpenAPI spec research and unverified against a live GHL API. If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:**` marker to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.
