# Contacts API — Tags

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Add Tags

Add one or more tags to a contact.

**Method:** `POST`
**URL:** `/contacts/{contactId}/tags`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `tags` | array | Yes | Array of tag strings to add |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/tags' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "tags": ["high-priority", "needs-followup"]
  }'
```

### Response

Returns the updated contact object with HTTP status `200 OK`.

> **Note:** Tags are case-sensitive. Duplicate tags are automatically ignored.

---

## Remove Tags

Remove one or more tags from a contact.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}/tags`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `tags` | array | Yes | Array of tag strings to remove |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/tags' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "tags": ["old-tag", "expired"]
  }'
```

### Response

Returns the updated contact object with HTTP status `200 OK`.

---

## Bulk Update Tags

Add or remove tags for multiple contacts in a single operation.

**Method:** `POST`
**URL:** `/contacts/bulk/tags/update/{type}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `type` | string | Yes | Operation type: `add` or `remove` |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | Location ID. ⚠️ Required — omitting returns 400 "LocationId can't be undefined" |
| `contacts` | array | Yes | Array of contact IDs to update (max 500). ⚠️ The field name is `contacts`, NOT `contactIds` — using `contactIds` returns a 422 error. |
| `tags` | array | Yes | Array of tag strings to add/remove |

### Request Example (Add Tags)

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/bulk/tags/update/add' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_xyz789",
    "contacts": [
      "contact_abc123",
      "contact_def456",
      "contact_ghi789"
    ],
    "tags": ["webinar-attendee", "q1-2026"]
  }'
```

### Request Example (Remove Tags)

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/bulk/tags/update/remove' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_xyz789",
    "contacts": [
      "contact_abc123",
      "contact_def456"
    ],
    "tags": ["old-campaign", "expired"]
  }'
```

> **Tested:** `contacts` (not `contactIds`) and `locationId` are required fields.

### Response Example

```json
{
  "success": true,
  "message": "Tags updated successfully",
  "processed": 3,
  "failed": 0,
  "errors": []
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether operation completed successfully |
| `message` | string | Human-readable result message |
| `processed` | number | Number of contacts successfully updated |
| `failed` | number | Number of contacts that failed to update |
| `errors` | array | Array of error objects for failed updates |

> **Note:** This operation is processed asynchronously. Large batches may take time to complete.
