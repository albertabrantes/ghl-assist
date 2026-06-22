# Calendars API — Calendar Resources (Deprecated, Services V1)

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Calendar resources cover rooms and equipment. See `core.md` for authentication, scopes, and rate limits.

> **Deprecation Notice:** All calendar resource endpoints have been deprecated and may be replaced or removed in future API versions. These are part of the Services V1 system. Resource types include rooms and equipment.

---

## List Calendar Resources

List calendar resources by resource type and location.

**Method:** `GET`
**URL:** `/calendars/resources/:resourceType`
**Scope:** `calendars/resources.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `resourceType` | string | Yes | Type of resource (e.g., `rooms`, `equipments`) |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/resources/rooms?locationId=LOCATION_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns a list of calendar resources with HTTP status `200 OK`.

---

## Get Calendar Resource

Get a single calendar resource by type and ID.

**Method:** `GET`
**URL:** `/calendars/resources/:resourceType/:id`
**Scope:** `calendars/resources.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `resourceType` | string | Yes | Type of resource |
| `id` | string | Yes | Resource ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/resources/rooms/RESOURCE_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns the resource object with HTTP status `200 OK`.

---

## Create Calendar Resource

Create a new calendar resource.

**Method:** `POST`
**URL:** `/calendars/resources/:resourceType`
**Scope:** `calendars/resources.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `resourceType` | string | Yes | Type of resource to create |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `name` | string | Yes | Name of the resource |
| `description` | string | No | Description of the resource |
| `quantity` | number | No | Available quantity |
| `outOfService` | boolean | No | Whether the resource is out of service |
| `capacity` | number | No | Capacity of the resource (rooms) |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/calendars/resources/rooms' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "location_id",
    "name": "Conference Room A",
    "capacity": 10
  }'
```

### Response

Returns the created resource with HTTP status `201 Created`.

---

## Update Calendar Resource

Update an existing calendar resource.

**Method:** `PUT`
**URL:** `/calendars/resources/:resourceType/:id`
**Scope:** `calendars/resources.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `resourceType` | string | Yes | Type of resource |
| `id` | string | Yes | Resource ID |

### Request Body

Same fields as [Create Calendar Resource](#create-calendar-resource). Only provided fields will be updated.

### Response

Returns the updated resource with HTTP status `200 OK`.

---

## Delete Calendar Resource

Delete a calendar resource by type and ID.

**Method:** `DELETE`
**URL:** `/calendars/resources/:resourceType/:id`
**Scope:** `calendars/resources.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `resourceType` | string | Yes | Type of resource |
| `id` | string | Yes | Resource ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/calendars/resources/rooms/RESOURCE_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns HTTP status `200 OK` on successful deletion.
