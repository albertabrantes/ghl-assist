# Locations API — Custom Values

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Get Custom Values

List all custom values in a location.

**Method:** `GET`
**URL:** `/locations/{locationId}/customValues`
**Scope:** `locations/customValues.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customValues' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "customValues": [
    {
      "id": "cv_abc123",
      "name": "Company Slogan",
      "fieldKey": "location.company_slogan",
      "value": "Your Success Is Our Business",
      "locationId": "ve9EPM428h8vShlRW1KT"
    }
  ],
  "traceId": "abc123-trace-id"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Get Custom Value

Retrieve a single custom value by ID.

**Method:** `GET`
**URL:** `/locations/{locationId}/customValues/{id}`
**Scope:** `locations/customValues.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The custom value ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customValues/cv_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns a single `customValue` object with the same schema as [Get Custom Values](#get-custom-values).

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Create Custom Value

Create a new custom value in a location.

**Method:** `POST`
**URL:** `/locations/{locationId}/customValues`
**Scope:** `locations/customValues.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Display name/label |
| `value` | string | Yes | The actual value content |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customValues' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Office Hours",
    "value": "Mon-Fri 9am-5pm CST"
  }'
```

### Response Example

```json
{
  "customValue": {
    "id": "cv_xyz789",
    "name": "Office Hours",
    "fieldKey": "location.office_hours",
    "value": "Mon-Fri 9am-5pm CST",
    "locationId": "ve9EPM428h8vShlRW1KT"
  },
  "traceId": "abc123-trace-id"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Update Custom Value

Update an existing custom value.

**Method:** `PUT`
**URL:** `/locations/{locationId}/customValues/{id}`
**Scope:** `locations/customValues.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The custom value ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | No | Display name/label |
| `value` | string | No | The actual value content |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customValues/cv_xyz789' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "value": "Mon-Fri 8am-6pm CST"
  }'
```

### Response

Returns the updated `customValue` object.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Delete Custom Value

Delete a custom value from a location.

**Method:** `DELETE`
**URL:** `/locations/{locationId}/customValues/{id}`
**Scope:** `locations/customValues.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The custom value ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/customValues/cv_xyz789' \
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
