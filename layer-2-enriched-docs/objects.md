# GHL Custom Objects API

> **Status:** All GET and POST /records/search endpoints verified with live API calls. See Known Issues section below before implementing.

---

## Overview

The Objects API manages GHL's custom object system: schema introspection, record CRUD, and search. Both system-defined objects (Contact, Opportunity, Business/Company) and user-defined custom objects (created by the account owner) are accessible here.

**Base URL:** `https://services.leadconnectorhq.com`  
**Version header:** `Version: 2021-07-28`

---

## Required Scopes

| Operation | Scope |
|---|---|
| Read object schemas | `objects/schema.readonly` |
| Write/update object schemas | `objects/schema.write` |
| Read records | `objects/record.readonly` |
| Write records | `objects/record.write` |
| Create new custom object type | Agency-level token + `objects/schema.write` |

---

## Endpoints

### Get All Objects for a Location

Lists all objects (system-defined + user-defined) for a sub-account.

**GET** `/objects/?locationId={locationId}`

**Required scopes:** `objects/schema.readonly`

**Query params:**

| Param | Required | Description |
|---|---|---|
| `locationId` | Yes | The sub-account location ID |

**Response shape:**

```json
{
  "objects": [
    {
      "id": "...",
      "labels": { "singular": "Company", "plural": "Companies" },
      "description": "...",
      "requiredProperties": ["business.name"],
      "searchableProperties": ["business.name", "business.email"],
      "primaryDisplayProperty": "business.name",
      "key": "business",
      "locationId": "...",
      "updatedAt": "...",
      "createdAt": "...",
      "type": "SYSTEM_DEFINED",
      "icon": { "class": "...", "svg": "..." }
    }
  ],
  "cache": true,
  "traceId": "..."
}
```

**Object types:** `SYSTEM_DEFINED` (Contact, Opportunity, Business) or `USER_DEFINED` (custom objects).

**Custom object keys** follow the pattern `custom_objects.{slug}` (e.g., `custom_objects.touchpoints`, `custom_objects.partnership_agencies`).

> **Tested:** Confirmed response shape. `cache: true` is returned on both first and subsequent calls.

---

### Get Object Schema by Key

Fetches schema for a single object. Add `fetchProperties=true` to include all field definitions.

**GET** `/objects/{key}?locationId={locationId}&fetchProperties=true`

**Required scopes:** `objects/schema.readonly`

**Path params:**

| Param | Required | Description |
|---|---|---|
| `key` | Yes | The object key, e.g. `business`, `contact`, `custom_objects.touchpoints` |

**Query params:**

| Param | Required | Description |
|---|---|---|
| `locationId` | Yes | The sub-account location ID |
| `fetchProperties` | No | Pass `true` to include field definitions in the `fields` array |

**Response shape (with fetchProperties=true):**

```json
{
  "object": { ...same as in list... },
  "fields": [
    {
      "id": "...",
      "standard": false,
      "locationId": "...",
      "fieldKey": "custom_objects.touchpoints.call_type",
      "name": "Call Type",
      "dataType": "TEXT",
      "position": 300,
      "showInForms": true,
      "parentId": "...",
      "objectKey": "custom_objects.touchpoints",
      "options": [...],
      "dateAdded": "...",
      "dateUpdated": "..."
    }
  ],
  "cache": true,
  "traceId": "..."
}
```

**Field dataTypes observed in production:**

| dataType | Notes |
|---|---|
| `TEXT` | Single-line text |
| `LARGE_TEXT` | Multi-line / rich text |
| `NUMERICAL` | Number |
| `MONETORY` | Currency. **Note the typo: `MONETORY` not `monetary`.** This is a GHL production bug; match on the misspelled string. Value structure: `{"currency": "default", "value": 2240}` |
| `CHECKBOX` | Boolean checkbox. Has `options` array (e.g., `[{"key": "yes", "label": "Yes"}]`) |
| `SINGLE_OPTIONS` | Dropdown with predefined options. Has `options` array |
| `EMAIL` | Email field |
| `PHONE` | Phone number |
| `STANDARD_FIELD` | System-managed field (appears on Opportunity object) |

> **Tested:** Confirmed `fetchProperties=true` returns `fields` array. Confirmed `MONETORY` typo in production. The `standard: true/false` flag distinguishes system-provided fields from user-created ones.

---

### Search Object Records

Queries records for a given object. This is the primary way to list records (there is no plain GET list endpoint).

**POST** `/objects/{schemaKey}/records/search`

**Required scopes:** `objects/record.readonly`

**Path params:**

| Param | Required | Description |
|---|---|---|
| `schemaKey` | Yes | Object key, e.g. `custom_objects.partnership_agencies` |

**Request body:**

```json
{
  "locationId": "...",
  "page": 1,
  "pageLimit": 5
}
```

> **CRITICAL GOTCHA:** The param is `pageLimit`, NOT `pageSize`. Sending `pageSize` returns HTTP 422: `"property pageSize should not exist"`, `"pageLimit should not be empty"`. This is a schema validation error, not a soft fallback.

**Response shape:**

```json
{
  "records": [
    {
      "id": "...",
      "locationId": "...",
      "objectId": "...",
      "objectKey": "custom_objects.partnership_agencies",
      "createdBy": {
        "source": "INTEGRATION",
        "channel": "OAUTH",
        "sourceId": "...",
        "createdAt": "..."
      },
      "createdAt": "...",
      "lastUpdatedBy": { ... },
      "updatedAt": "...",
      "owners": [],
      "followers": [],
      "properties": {
        "relationship_id": "0-000-000",
        "agency_name": "Example Agency Inc.",
        "pp_start_date": "2026-05-09"
      },
      "relations": [
        {
          "associationId": "...",
          "relationId": "...",
          "objectKey": "business",
          "recordId": "...",
          "createdAt": "..."
        }
      ],
      "sort": [1778692000876, "..."],
      "searchAfter": [1778692000876, "..."]
    }
  ],
  "total": 128,
  "traceId": "..."
}
```

**Pagination:** Use `searchAfter` from the last record of each page to paginate forward. The `total` field gives the full count.

**Creation sources observed:**
- `WORKFLOW_NEW` / `ISTIO_MESH` — created automatically by a GHL workflow
- `INTEGRATION` / `OAUTH` — created by an API integration (custom script)
- `WEB_USER` / `APP` — created manually in the GHL UI
- `FORM` / `ISTIO_MESH` — created from a form submission

> **Tested:** Confirmed `pageLimit` is required. Confirmed `total` count, `relations` array, and `searchAfter` cursor in live data.

---

### Get Record by ID

Fetches a single record by its ID.

**GET** `/objects/{schemaKey}/records/{id}?locationId={locationId}`

**Required scopes:** `objects/record.readonly` (inferred; no explicit scope in spec for GET)

**Path params:**

| Param | Required | Description |
|---|---|---|
| `schemaKey` | Yes | Object key |
| `id` | Yes | The record's MongoDB ID |

**Query params:**

| Param | Required | Description |
|---|---|---|
| `locationId` | Yes | Sub-account location ID |

**Response shape:**

```json
{
  "record": {
    "properties": {
      "name": "Example Name",
      "email": "...",
      "status": "subscription_required",
      "wl_location_id": "EXAMPLELocation00000",
      "agency_relationship_id": "0-000-000",
      "agency_name": "Example Agency",
      "pp_start_date": "May 13 2026"
    },
    "objectKey": "business",
    "owners": [],
    "follower": [],
    "relations": [],
    "locationId": "...",
    "createdBy": { ... },
    "createdAt": "...",
    "updatedAt": "...",
    "lastUpdatedBy": { ... },
    "id": "..."
  },
  "traceId": "..."
}
```

> **Tested:** Confirmed response shape. Note: the list/search endpoint uses `followers` (plural) but the single-record GET returns `follower` (singular) — inconsistency in GHL API.

---

### Create Record

Creates a new record for a custom object.

**POST** `/objects/{schemaKey}/records`

**Required scopes:** `objects/record.write`

**Request body:** (properties vary by object schema)

```json
{
  "locationId": "...",
  "properties": {
    "field_key_without_prefix": "value"
  }
}
```

> **Not tested live**. Schema from official spec.

---

### Update Record

Updates an existing record.

**PUT** `/objects/{schemaKey}/records/{id}?locationId={locationId}`

**Required scopes:** (not explicitly scoped in spec; write access assumed)

> **Not tested live**. Schema from official spec.

---

### Delete Record

Deletes a record permanently.

**DELETE** `/objects/{schemaKey}/records/{id}`

**Required scopes:** (not explicitly scoped in spec)

> **Not tested live**.

---

### Create Custom Object Type

Creates a new user-defined object schema.

**POST** `/objects/`

**Required:** Agency-level token + `objects/schema.write`

> **Note:** This is an agency-level operation. A location-scope PIT cannot create new object types.

---

### Update Object Schema

Updates an existing object's schema (labels, required properties, searchable properties).

**PUT** `/objects/{key}`

**Required scopes:** `objects/schema.write`

> **Not tested live** (read-only audit; schema writes would be destructive on a live account).

---

## Known Issues & Gotchas

### 1. `pageSize` vs `pageLimit` (VERIFIED)

The search endpoint requires `pageLimit`, not `pageSize`. Sending `pageSize` returns HTTP 422 with three validation messages:
- `"property pageSize should not exist"`
- `"pageLimit should not be empty"`
- `"pageLimit must be a number conforming to the specified constraints"`

This is a schema validation error with no soft fallback. Every caller (HTTP client, curl, Python, etc.) must use `pageLimit`.

### 2. `MONETORY` typo in field types (VERIFIED)

Currency field types are `MONETORY` in production — not `monetary`, not `currency`. This is a GHL production bug present in field schema responses. The value structure for these fields is `{"currency": "default", "value": 2240}`. Match on the misspelled string.

### 3. `follower` vs `followers` inconsistency (VERIFIED)

The search/list endpoint returns `followers` (array, plural). The single-record GET endpoint returns `follower` (array, singular). Both are arrays. Parse defensively with fallbacks.

### 4. Date format inconsistency across objects (VERIFIED)

The same date field (same name, same intent) can appear in two formats across related objects:
- One object's records: `"2026-05-09"` (ISO date)
- Linked records in another object: `"May 13 2026"` (verbose English)

This is not a timezone issue — the two are set by different integration paths. Normalize to ISO before comparing or sorting.

### 5. TEXT fields with no dropdown (VERIFIED)

Some fields appear to be intended as enums but are defined as plain `TEXT` with no `options` array. Actual values in use are determined by whatever the workflow or integration writes — there is no API-level validation of the string value.

### 6. Business records can have two distinct populations (VERIFIED)

The `business` object may be used for two separate things in the same sub-account:
1. **Sub-account records** (e.g. created by an OAUTH integration): fully populated with your custom fields.
2. **Form submission records** (e.g. created by a FORM source): only `name` is set. Extended fields are populated separately by a workflow/integration.

Filter by `createdBy.source` if you need to distinguish these populations.

### 7. Record creation sources (VERIFIED)

Observed values for `createdBy.source`:
- `WORKFLOW_NEW` — GHL workflow automation
- `INTEGRATION` — External OAuth integration (API script)
- `WEB_USER` — Manual entry via GHL UI
- `FORM` — GHL form submission

`createdBy.channel` values: `ISTIO_MESH` (internal GHL mesh), `OAUTH` (external integration), `APP` (GHL web app).

---

## Data Model: Worked Example

Custom objects let you model domain data beyond contacts/opportunities and relate records together. The pattern below is a generic illustration of how a multi-object schema and its join logic typically look.

### A parent custom object (`custom_objects.accounts`)

| Field | Key | Type | Notes |
|---|---|---|---|
| Relationship ID | `custom_objects.accounts.relationship_id` | TEXT | Unique. Foreign key other records link to. |
| Account Name | `custom_objects.accounts.account_name` | TEXT | |
| Start Date | `custom_objects.accounts.start_date` | TEXT | ISO format, e.g. `2026-05-09` |
| Owner Contact ID | `custom_objects.accounts.owner_contact_id` | TEXT | GHL contact ID of the account owner |
| Status | `custom_objects.accounts.status` | TEXT | Free-text or workflow-injected |
| Credit | `custom_objects.accounts.credit` | MONETORY | e.g. `{"currency":"default","value":2240}` |

### A child custom object (`custom_objects.touchpoints`)

| Field | Key | Type | Notes |
|---|---|---|---|
| Contact ID | `custom_objects.touchpoints.contact_id` | TEXT | GHL contact ID of the end-customer |
| Notes | `custom_objects.touchpoints.notes` | LARGE_TEXT | Free-text; can hold long call summaries |
| Disposition | `custom_objects.touchpoints.disposition` | TEXT | e.g. "Call - Showed", "Call - Reschedule" |
| Recording Link | `custom_objects.touchpoints.recording_link` | TEXT | URL, or "n/a" |
| Location ID | `custom_objects.touchpoints.location_id` | TEXT | GHL location ID this record belongs to |

### Joining the system `business` object

Custom fields can also be added to the system-defined `business` object to link it back to your custom objects:

| Field | Key | Type | Notes |
|---|---|---|---|
| Account Relationship ID | `business.account_relationship_id` | TEXT | Foreign key to `custom_objects.accounts.relationship_id` |
| Location ID | `business.location_id` | TEXT | GHL location ID of this sub-account |
| Status | `business.status` | SINGLE_OPTIONS | Your own option set |

### Join logic for dashboard queries

```
Accounts  ──[relationship_id = account_relationship_id]──▶  Business (sub-accounts)
                                                                    │
                                                          [location_id = location_id]
                                                                    │
                                                             Touchpoints (history)
```

Step 1: Search the parent object to get the list and relationship IDs.
Step 2: Search Business records filtering by `account_relationship_id` to get sub-accounts + status.
Step 3: Search the child object filtering by `location_id` to get history per sub-account.
