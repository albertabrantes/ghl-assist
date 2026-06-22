# Locations API — Tags & Templates

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Get Tags

List all tags in a location.

**Method:** `GET`
**URL:** `/locations/{locationId}/tags`
**Scope:** `locations/tags.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/tags' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "tags": [
    {
      "id": "tag_abc123",
      "name": "VIP Client",
      "locationId": "ve9EPM428h8vShlRW1KT"
    },
    {
      "id": "tag_def456",
      "name": "New Lead",
      "locationId": "ve9EPM428h8vShlRW1KT"
    }
  ],
  "traceId": "abc123-trace-id"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Get Tag by ID

Retrieve a single tag by ID.

**Method:** `GET`
**URL:** `/locations/{locationId}/tags/{tagId}`
**Scope:** `locations/tags.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `tagId` | string | Yes | The tag ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/tags/tag_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns a single `tag` object with the same schema as [Get Tags](#get-tags).

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Create Tag

Create a new tag in a location.

**Method:** `POST`
**URL:** `/locations/{locationId}/tags`
**Scope:** `locations/tags.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Tag name |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/tags' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{"name": "Hot Lead"}'
```

### Response Example

```json
{
  "tag": {
    "id": "tag_ghi789",
    "name": "Hot Lead",
    "locationId": "ve9EPM428h8vShlRW1KT"
  },
  "traceId": "abc123-trace-id"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Update Tag

Update an existing tag.

**Method:** `PUT`
**URL:** `/locations/{locationId}/tags/{tagId}`
**Scope:** `locations/tags.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `tagId` | string | Yes | The tag ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Updated tag name |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/tags/tag_ghi789' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{"name": "Very Hot Lead"}'
```

### Response

Returns the updated `tag` object.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Delete Tag

Delete a tag from a location.

**Method:** `DELETE`
**URL:** `/locations/{locationId}/tags/{tagId}`
**Scope:** `locations/tags.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `tagId` | string | Yes | The tag ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/tags/tag_ghi789' \
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

## Get Templates

List email, SMS, and WhatsApp templates for a location.

**Method:** `GET`
**URL:** `/locations/{locationId}/templates`
**Scope:** `locations/templates.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `type` | string | Yes | Template type: `sms`, `email`, or `whatsapp` |
| `deleted` | boolean | No | Include soft-deleted templates. Default: `false` |
| `origin` | string | No | Filter by template origin |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/templates?type=email' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "templates": [
    {
      "id": "tmpl_abc123",
      "name": "Welcome Email",
      "type": "email",
      "body": "<html><body>Welcome!</body></html>",
      "subject": "Welcome to Our Service!",
      "attachments": [],
      "locationId": "ve9EPM428h8vShlRW1KT",
      "createdAt": "2024-01-15T10:00:00.000Z",
      "updatedAt": "2024-06-20T15:00:00.000Z"
    }
  ],
  "traceId": "abc123-trace-id"
}
```

### Important Notes

- No `locations/templates.write` scope exists — there is no API endpoint for **creating** templates. Only listing and deleting are supported.
- SMS templates have a `body` field but no `subject`.
- Templates created with the newer email builder may have limited API access (known limitation).

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Delete Template

Delete an email or SMS template.

**Method:** `DELETE`
**URL:** `/locations/{locationId}/templates/{id}`
**Scope:** `locations/templates.readonly` (uses the readonly scope despite being a delete operation — confirmed in the official docs)

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The template ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/templates/tmpl_abc123' \
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
