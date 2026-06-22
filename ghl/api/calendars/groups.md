# Calendars API — Calendar Groups

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Calendar groups group calendars together with a shared booking page. See `core.md` for authentication, scopes, and rate limits.

---

## Create Calendar Group

Create a new calendar group for grouping calendars together with a shared booking page.

**Method:** `POST`
**URL:** `/calendars/groups`
**Scope:** `calendars/groups.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `name` | string | Yes | Name of the calendar group |
| `description` | string | No | Description of the group |
| `slug` | string | No | URL slug for the group booking page |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/calendars/groups' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "location_id",
    "name": "Sales Team Calendars",
    "slug": "sales-team"
  }'
```

### Response

Returns the created calendar group with HTTP status `201 Created`.

---

## Validate Group Slug

Validate whether a calendar group slug is available.

**Method:** `POST`
**URL:** `/calendars/groups/validate-slug`
**Scope:** `calendars/groups.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `slug` | string | Yes | The slug to validate |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/calendars/groups/validate-slug' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "location_id",
    "slug": "sales-team"
  }'
```

### Response

Returns slug availability status with HTTP status `200 OK`.

---

## Delete Calendar Group

Delete a calendar group by ID.

**Method:** `DELETE`
**URL:** `/calendars/groups/:groupId`
**Scope:** `calendars/groups.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `groupId` | string | Yes | The calendar group ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/calendars/groups/GROUP_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns HTTP status `200 OK` on successful deletion.
