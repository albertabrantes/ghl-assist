# Contacts API — Search

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Search Contacts

Search and filter contacts using advanced criteria.

**Method:** `POST`
**URL:** `/contacts/search`
**Scope:** `contacts.readonly`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | Location to search within |
| `query` | string | No | Search term (matches name, email, phone, company) |
| `tags` | array | No | Filter by tags (OR logic) |
| `assignedTo` | string | No | Filter by assigned user ID |
| `contactType` | string | No | Filter by type: `lead`, `customer`, `prospect` |
| `pageLimit` | number | No | Results per page (max 100, default 20). ⚠️ The field name is `pageLimit`, NOT `limit` — using `limit` returns a 422 error. |
| `startAfter` | string | No | Pagination cursor from previous response |
| `sort` | object | No | Sort configuration |
| `sort.field` | string | No | Field to sort by (e.g., `createdAt`, `updatedAt`, `name`) |
| `sort.direction` | string | No | Sort order: `asc` or `desc` |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/search' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "loc_xyz789",
    "query": "acme",
    "tags": ["customer", "vip"],
    "pageLimit": 50,
    "sort": {
      "field": "createdAt",
      "direction": "desc"
    }
  }'
```

### Response Example

```json
{
  "contacts": [
    {
      "id": "contact_abc123",
      "locationId": "loc_xyz789",
      "firstName": "John",
      "lastName": "Smith",
      "email": "john.smith@example.com",
      "phone": "+1234567890",
      "companyName": "Acme Corporation",
      "tags": ["customer", "vip"],
      "createdAt": "2026-01-15T10:30:00Z"
    },
    {
      "id": "contact_def456",
      "locationId": "loc_xyz789",
      "firstName": "Jane",
      "lastName": "Doe",
      "email": "jane@acme.com",
      "phone": "+14155551234",
      "companyName": "Acme Industries",
      "tags": ["customer"],
      "createdAt": "2026-01-10T08:15:00Z"
    }
  ],
  "meta": {
    "total": 150,
    "currentPage": 1,
    "nextCursor": "cursor_xyz123",
    "hasMore": true
  }
}
```
