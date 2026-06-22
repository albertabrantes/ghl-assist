# Forms API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Forms API provides **read access to forms and submissions**, plus a file upload endpoint for custom fields. It has **3 endpoints**. Form submissions **auto-create contacts** when no matching contact exists (matched by email/phone). There is no API to submit forms programmatically — that is UI-only.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `forms.readonly` | Read | List forms and retrieve form submissions |
| `forms.write` | Write | Upload files to contact custom fields |

---

## Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get Forms](#get-forms) | GET | readonly | List all forms in a location |
| [Get Forms Submissions](#get-forms-submissions) | GET | readonly | List form submissions with filtering |
| [Upload Files to Custom Fields](#upload-files-to-custom-fields) | POST | write | Upload files to contact custom fields |

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

### Form Object

```json
{
  "id": "EXAMPLEForm00000001",
  "name": "Contact Us Form",
  "locationId": "ve9EPM428h8vShlRW1KT"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique form identifier |
| `name` | string | Form name |
| `locationId` | string | Location/sub-account ID |

### Form Submission Object

```json
{
  "id": "sub_abc123def456",
  "contactId": "ctc_xyz789ghi012",
  "createdAt": "2024-06-15T14:30:00.000Z",
  "formId": "EXAMPLEForm00000002",
  "name": "John Doe",
  "email": "john@example.com",
  "others": {
    "phone": "+1234567890",
    "company": "Acme Corp",
    "message": "I'm interested in your services",
    "custom_field_abc": "Custom value"
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Submission ID |
| `contactId` | string | Associated contact ID (auto-created or matched) |
| `createdAt` | string | ISO 8601 submission timestamp |
| `formId` | string | ID of the submitted form |
| `name` | string | Contact's submitted name |
| `email` | string | Contact's submitted email |
| `others` | object | Key-value map of all additional submitted fields (custom field IDs to values) |

---

## Get Forms

List all forms in a location.

**Method:** `GET`
**URL:** `/forms/`
**Scope:** `forms.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `skip` | integer | No | Records to skip (offset pagination, default 0) |
| `limit` | integer | No | Max forms to return (default 20) |
| `type` | string | No | Filter by form type (e.g., `"folder"`) |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/forms/?locationId=ve9EPM428h8vShlRW1KT&limit=20&skip=0' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "forms": [
    {
      "id": "EXAMPLEForm00000001",
      "name": "Contact Us Form",
      "locationId": "ve9EPM428h8vShlRW1KT"
    },
    {
      "id": "EXAMPLEForm00000002",
      "name": "Lead Capture Form",
      "locationId": "ve9EPM428h8vShlRW1KT"
    }
  ],
  "total": 2
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `forms` | array | Array of form objects |
| `total` | number | Total number of forms |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Get Forms Submissions

List submissions with optional filtering by form, date range, and search query.

**Method:** `GET`
**URL:** `/forms/submissions`
**Scope:** `forms.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `page` | integer | No | Page number (starts at 1, default 1) |
| `limit` | integer | No | Results per page (default 20, max 100) |
| `formId` | string | No | Filter by specific form ID |
| `q` | string | No | Search query (email, name, etc.) |
| `startAt` | string | No | Start date filter (YYYY-MM-DD) |
| `endAt` | string | No | End date filter (YYYY-MM-DD) |

**Note on pagination:** Submissions use **page/limit** (page-number-based), not skip/limit. This differs from the form listing endpoint.

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/forms/submissions?locationId=ve9EPM428h8vShlRW1KT&page=1&limit=20&formId=EXAMPLEForm00000002&startAt=2024-01-01&endAt=2024-12-31' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "submissions": [
    {
      "id": "sub_abc123def456",
      "contactId": "ctc_xyz789ghi012",
      "createdAt": "2024-06-15T14:30:00.000Z",
      "formId": "EXAMPLEForm00000002",
      "name": "John Doe",
      "email": "john@example.com",
      "others": {
        "phone": "+1234567890",
        "company": "Acme Corp",
        "message": "I'm interested in your services",
        "custom_field_abc": "Custom value"
      }
    }
  ],
  "meta": {
    "total": 150,
    "currentPage": 1,
    "nextPage": 2,
    "prevPage": null
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `submissions` | array | Array of form submission objects |
| `meta.total` | number | Total number of submissions matching the query |
| `meta.currentPage` | number | Current page number |
| `meta.nextPage` | number/null | Next page number, or null if last page |
| `meta.prevPage` | number/null | Previous page number, or null if first page |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Upload Files to Custom Fields

Upload files to a contact's FILE_UPLOAD custom fields via multipart form-data. Despite the `/forms/` path, this endpoint has nothing to do with forms. It uploads files directly to a contact's custom field and is the primary way to get files into a contact's **Documents tab**.

For the full file upload guide covering all methods (contacts, opportunities, conversations, media library), see **[file-uploads.md](file-uploads.md)**.

**Method:** `POST`
**URL:** `/forms/upload-custom-files`
**Scope:** `forms.write`
**Content-Type:** `multipart/form-data`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |
| `contactId` | string | Yes | Contact to attach files to |

**Note:** This endpoint only accepts `contactId`. It does NOT accept `opportunityId` (returns 422). To upload files to opportunity custom fields, see the two-step workaround in [file-uploads.md](file-uploads.md#method-2-opportunity-custom-field-upload-two-step).

### Request Body (multipart/form-data)

Files are sent as form-data fields with keys in the format `{custom_field_id}_{file_id}`:

| Field Key Format | Type | Description |
|------------------|------|-------------|
| `{custom_field_id}_{file_id}` | binary | File upload. `custom_field_id` is the ID of a FILE_UPLOAD type custom field, `file_id` is a generated UUID |

**Constraints:**
- **Max file size:** 50 MB per file
- **Allowed types:** PDF, DOCX, DOC, JPG, JPEG, PNG, GIF, CSV, XLSX, XLS, MP4, MPEG, ZIP, RAR, TXT, SVG
- Multiple files supported via multiple form-data fields

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/forms/upload-custom-files?contactId=EXAMPLEContact000001&locationId=EXAMPLELocation00001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -F 'EXAMPLECustomField01_550e8400-e29b-41d4-a716-446655440000=@/path/to/document.pdf'
```

### Response Example

```json
{
  "succeded": true,
  "contact": {
    "id": "EXAMPLEContact000001",
    "customFields": {
      "EXAMPLECustomField01": {
        "<uuid>": {
          "meta": {
            "fieldname": "EXAMPLECustomField01",
            "originalname": "document.pdf",
            "encoding": "7bit",
            "mimetype": "application/pdf",
            "size": 4070640,
            "uuid": "<uuid>"
          },
          "url": "https://services.leadconnectorhq.com/documents/download/<documentId>",
          "documentId": "<documentId>"
        }
      }
    }
  }
}
```

Note: GHL misspells "succeeded" as `"succeded"` in the response. Plan for this in code.

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `succeded` | boolean | Success flag (yes, it's misspelled by GHL) |
| `contact` | object | The full updated contact object |
| `contact.customFields` | object | Map of custom field IDs to their values, including uploaded file metadata and download URLs |

### Where Files Appear in GHL UI

Uploaded files appear in: **Contact record > Documents tab > Internal > Custom Fields** folder.

### Multiple Uploads to Same Field

- **Custom field value:** Only stores the latest file reference (overwrites previous)
- **Documents tab:** Keeps ALL files ever uploaded. Files accumulate even after the field value is overwritten.

This is ideal for versioning: upload new versions to the same field, the Documents tab retains full history, and the field always points to the latest.

> **Tested:** Verified with live API using a Private Integration Token. Single upload, multiple uploads, and Documents tab accumulation behavior all confirmed.

---

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 400 | Bad Request — invalid parameters or invalid file type/size |
| 401 | Unauthorized — invalid/missing token or insufficient scopes |
| 422 | Unprocessable Entity — validation failure |

---

## Notes

- Form submissions **auto-create contacts** if no existing contact matches by email or phone. If a match exists, the submission links to the existing contact. Each submission's `contactId` field links to the resulting GHL contact record.
- The `others` object on submissions is a flat key-value map. Standard fields (name, email) are top-level; all custom fields go into `others`.
- There is **no API to submit forms programmatically** — form submission is UI-only.
- The Get Forms endpoint uses **skip/limit** pagination, while Get Forms Submissions uses **page/limit** (page-number-based) pagination. These are not interchangeable.
- The file upload endpoint requires `multipart/form-data` content type, not JSON. The field key format uses the custom field ID combined with a UUID file identifier.

---

> **Agent Note:** Endpoints marked 🔬 are documented from OpenAPI spec research and unverified against a live GHL API. If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:**` marker to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.
