# Locations API — Custom Fields

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

These are **location-level** custom fields (legacy API). Do not confuse with the Custom Fields V2 API at `/custom-fields/`, which is a separate, newer API with folder support. Both use the same OAuth scopes.

## Get Custom Fields

List all custom fields defined in a location.

**Method:** `GET`
**URL:** `/locations/{locationId}/customFields`
**Scope:** `locations/customFields.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `model` | string | No | Filter by model: `contact`, `opportunity`, or `all` |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customFields?model=contact' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "customFields": [
    {
      "id": "cf_abc123",
      "name": "Preferred Contact Method",
      "fieldKey": "contact.preferred_contact_method",
      "dataType": "SINGLE_OPTIONS",
      "placeholder": "Select preferred method",
      "position": 0,
      "picklistOptions": ["Phone", "Email", "Text"],
      "isAllowedCustomOption": false,
      "isMultiFileAllowed": false,
      "maxFileLimit": 0,
      "locationId": "ve9EPM428h8vShlRW1KT",
      "model": "contact"
    }
  ],
  "traceId": "abc123-trace-id"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Get Custom Field

Retrieve a single custom field by ID.

**Method:** `GET`
**URL:** `/locations/{locationId}/customFields/{id}`
**Scope:** `locations/customFields.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The custom field ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customFields/cf_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "customField": {
    "id": "cf_abc123",
    "name": "Preferred Contact Method",
    "fieldKey": "contact.preferred_contact_method",
    "dataType": "SINGLE_OPTIONS",
    "placeholder": "Select preferred method",
    "position": 0,
    "picklistOptions": ["Phone", "Email", "Text"],
    "isAllowedCustomOption": false,
    "isMultiFileAllowed": false,
    "maxFileLimit": 0,
    "locationId": "ve9EPM428h8vShlRW1KT",
    "model": "contact"
  },
  "traceId": "abc123-trace-id"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Create Custom Field

Create a new custom field in a location.

**Method:** `POST`
**URL:** `/locations/{locationId}/customFields`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Display name |
| `dataType` | string | Yes | Field data type (see enum below) |
| `placeholder` | string | No | Placeholder text |
| `position` | integer | No | Display position/order |
| `model` | string | No | Object model: `contact`, `opportunity` |
| `acceptedFormat` | string[] | No | Accepted file formats (for FILE_UPLOAD) |
| `isMultiFileAllowed` | boolean | No | Allow multiple files |
| `maxFileLimit` | integer | No | Max file count |
| `isAllowedCustomOption` | boolean | No | Allow custom options for dropdowns |
| `picklistOptions` | string[] | No | Options for SINGLE_OPTIONS / MULTIPLE_OPTIONS |
| `picklistImageOptions` | object[] | No | Image-based options |

**`dataType` enum values:**

`TEXT`, `LARGE_TEXT`, `NUMERICAL`, `PHONE`, **`MONETORY`** (intentional spelling), `CHECKBOX`, `SINGLE_OPTIONS`, `MULTIPLE_OPTIONS`, `FLOAT`, `TIME`, `DATE`, `TEXTBOX_LIST`, `FILE_UPLOAD`, `SIGNATURE`, `RADIO`

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customFields' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Lead Source",
    "dataType": "SINGLE_OPTIONS",
    "placeholder": "Select source",
    "position": 0,
    "model": "contact",
    "picklistOptions": ["Google Ads", "Facebook", "Referral", "Walk-in"]
  }'
```

### Response Example

```json
{
  "customField": {
    "id": "cf_xyz789",
    "name": "Lead Source",
    "fieldKey": "contact.lead_source",
    "dataType": "SINGLE_OPTIONS",
    "placeholder": "Select source",
    "position": 0,
    "picklistOptions": ["Google Ads", "Facebook", "Referral", "Walk-in"],
    "locationId": "ve9EPM428h8vShlRW1KT",
    "model": "contact",
    "isAllowedCustomOption": false
  },
  "traceId": "abc123-trace-id"
}
```

### Important Notes

- The `fieldKey` is auto-generated from the name and used to reference the field in contact/opportunity records.
- **`MONETORY` is intentionally misspelled** — this is a known quirk in the API, not a typo. Use exactly as spelled.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Update Custom Field

Update an existing custom field.

**Method:** `PUT`
**URL:** `/locations/{locationId}/customFields/{id}`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The custom field ID |

### Request Body

Same fields as [Create Custom Field](#create-custom-field), all optional. Include only fields to update.

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customFields/cf_xyz789' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Updated Lead Source",
    "picklistOptions": ["Google Ads", "Facebook", "Referral", "Walk-in", "Website"]
  }'
```

### Response

Returns the updated `customField` object.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Delete Custom Field

Delete a custom field from a location.

**Method:** `DELETE`
**URL:** `/locations/{locationId}/customFields/{id}`
**Scope:** `locations/customFields.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The custom field ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customFields/cf_xyz789' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "success": true,
  "traceId": "abc123-trace-id"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Upload File to Custom Fields

Upload a file for use with FILE_UPLOAD type custom fields.

**Method:** `POST`
**URL:** `/locations/{locationId}/customFields/upload`
**Scope:** `locations/customFields.write`
**Content-Type:** `multipart/form-data`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customFields/upload' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -F 'file=@/path/to/document.pdf'
```

### Important Notes

- This endpoint accepts `multipart/form-data` rather than JSON.
- Exact form field names should be verified against the OpenAPI spec.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.
