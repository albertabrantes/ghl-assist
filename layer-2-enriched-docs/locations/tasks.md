# Locations API — Tasks, Recurring Tasks & Timezones

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Task Search

Search and filter tasks within a location.

**Method:** `POST`
**URL:** `/locations/{locationId}/tasks/search`
**Scope:** `locations/tasks.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `contactId` | string | No | Filter by contact ID |
| `status` | string | No | `completed` or `incompleted` |
| `assignedTo` | string | No | Filter by assigned user ID |
| `query` | string | No | Search by title or body text |
| `businessId` | string | No | Filter by business ID |
| `dueDate` | object | No | `{startDate: string, endDate: string}` (ISO 8601) |
| `limit` | integer | No | Results per page. Default: `20` |
| `skip` | integer | No | Results to skip. Default: `0` |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/tasks/search' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "status": "incompleted",
    "limit": 20,
    "skip": 0
  }'
```

### Response Example

```json
{
  "tasks": [
    {
      "id": "task_abc123",
      "title": "Follow up call",
      "body": "Call client to discuss proposal",
      "assignedTo": "user_xyz",
      "dueDate": "2024-06-20T15:00:00.000Z",
      "completed": false,
      "contactId": "contact_123",
      "locationId": "ve9EPM428h8vShlRW1KT",
      "createdAt": "2024-06-15T10:00:00.000Z",
      "updatedAt": "2024-06-15T10:00:00.000Z"
    }
  ],
  "count": 1,
  "traceId": "abc123-trace-id"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `tasks` | array | Array of task objects |
| `count` | number | Total matching results |
| `traceId` | string | Request trace identifier |

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Get Recurring Task

Retrieve a single recurring task by ID.

**Method:** `GET`
**URL:** `/locations/{locationId}/recurring-tasks/{id}`
**Scope:** `locations/tasks.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The recurring task ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/recurring-tasks/rt_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "recurringTask": {
    "id": "rt_abc123",
    "title": "Weekly Client Check-in",
    "body": "Check in with client about progress",
    "assignedTo": "user_xyz",
    "locationId": "ve9EPM428h8vShlRW1KT",
    "isRecurring": true,
    "intervalType": "weekly",
    "intervalValue": 1,
    "dueIn": {
      "time": 7,
      "unit": "days"
    },
    "createdAt": "2024-06-15T10:00:00.000Z"
  },
  "traceId": "abc123-trace-id"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Create Recurring Task

Create a new recurring task definition.

**Method:** `POST`
**URL:** `/locations/{locationId}/recurring-tasks`
**Scope:** `locations/tasks.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | Yes | Task title |
| `body` | string | No | Task description |
| `assignedTo` | string | Yes | User ID to assign to |
| `dueIn` | object | No | `{time: integer, unit: string}` — unit values: `minutes`, `hours`, `days` |
| `isRecurring` | boolean | No | Default: `true` |
| `intervalType` | string | No | `daily`, `weekly`, or `monthly` |
| `intervalValue` | integer | No | Recurrence interval (e.g., `2` for every 2 weeks) |
| `endDate` | string | No | ISO 8601 end date for recurrence |
| `contactId` | string | No | Associated contact ID |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/recurring-tasks' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "title": "Weekly Client Check-in",
    "body": "Review project progress",
    "assignedTo": "user_xyz",
    "dueIn": {"time": 7, "unit": "days"},
    "intervalType": "weekly",
    "intervalValue": 1
  }'
```

### Response

Returns the created `recurringTask` object (same schema as [Get Recurring Task](#get-recurring-task)).

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Update Recurring Task

Update an existing recurring task.

**Method:** `PUT`
**URL:** `/locations/{locationId}/recurring-tasks/{id}`
**Scope:** `locations/tasks.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The recurring task ID |

### Request Body

Same fields as [Create Recurring Task](#create-recurring-task), all optional. Include only fields to update.

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/recurring-tasks/rt_abc123' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "title": "Bi-Weekly Client Check-in",
    "intervalValue": 2
  }'
```

### Response

Returns the updated `recurringTask` object.

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.

---

## Delete Recurring Task

Delete a recurring task definition.

**Method:** `DELETE`
**URL:** `/locations/{locationId}/recurring-tasks/{id}`
**Scope:** `locations/tasks.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |
| `id` | string | Yes | The recurring task ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/recurring-tasks/rt_abc123' \
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

## Fetch Timezones

Retrieve the list of available IANA timezones.

**Method:** `GET`
**URL:** `/locations/{locationId}/timezones`
**Scope:** `locations.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/locations/ve9EPM428h8vShlRW1KT/timezones' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response Example

```json
{
  "timezones": [
    "America/New_York",
    "America/Chicago",
    "America/Denver",
    "America/Los_Angeles",
    "America/Phoenix",
    "Pacific/Honolulu",
    "Europe/London",
    "Asia/Tokyo"
  ],
  "traceId": "abc123-trace-id"
}
```

> **Status:** 🔬 Unverified — from OpenAPI spec / official docs.
