# Calendars API — Availability / Schedules

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

User and calendar availability schedule management. See `core.md` for authentication, scopes, and rate limits. For free-slot lookup on a specific calendar, see Get Free Slots in `core.md`.

---

## List User Availability Schedules

Search and retrieve user availability schedules based on filters including location, calendar, and user. Supports pagination.

**Method:** `GET`
**URL:** `/calendars/schedules/search`
**Scope:** `calendars.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `calendarId` | string | No | Filter by calendar |
| `userId` | string | No | Filter by user |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/schedules/search?locationId=LOCATION_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns a list of schedules with HTTP status `200 OK`.

---

## Get User Availability Schedule

Retrieve a specific schedule by its unique identifier. Returns detailed information including rules, timezone, and associated calendars/users.

**Method:** `GET`
**URL:** `/calendars/schedules/:id`
**Scope:** `calendars.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The schedule ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/schedules/SCHEDULE_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns the schedule object with rules, timezone, and associations. HTTP status `200 OK`.

---

## Get Event Calendar Availability Schedule

Retrieve the availability schedule for a specific event calendar. Returns the schedule associated with the calendar ID provided in the path.

**Method:** `GET`
**URL:** `/calendars/schedules/event-calendar/:calendarId`
**Scope:** `calendars.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `calendarId` | string | Yes | The event calendar ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/schedules/event-calendar/CALENDAR_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns the schedule for the event calendar. HTTP status `200 OK`. Returns `404` if calendar/schedule not found.

---

## Create User Availability Schedule

Create a new availability schedule with specified rules, timezone, location, user, and calendar associations.

**Method:** `POST`
**URL:** `/calendars/schedules`
**Scope:** `calendars.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `name` | string | Yes | Name of the schedule |
| `timezone` | string | Yes | Timezone (e.g., `America/New_York`) |
| `rules` | array | Yes | Availability rules (days, times) |
| `userId` | string | No | Associated user ID |
| `calendarId` | string | No | Associated calendar ID |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/calendars/schedules' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "location_id",
    "name": "Business Hours",
    "timezone": "America/New_York",
    "rules": [
      {
        "day": "monday",
        "startTime": "09:00",
        "endTime": "17:00"
      }
    ]
  }'
```

### Response

Returns the created schedule with HTTP status `201 Created`. Returns `422` on validation errors.

---

## Update User Availability Schedule

Update an existing schedule. Only provided fields will be updated.

**Method:** `PUT`
**URL:** `/calendars/schedules/:id`
**Scope:** `calendars.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The schedule ID |

### Request Body

Same fields as [Create User Availability Schedule](#create-user-availability-schedule). All fields are optional for updates.

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/calendars/schedules/SCHEDULE_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Updated Business Hours",
    "timezone": "America/Chicago"
  }'
```

### Response

Returns the updated schedule. HTTP status `200 OK`. Returns `404` if not found, `422` on validation errors.

---

## Delete User Availability Schedule

Permanently remove a schedule and all its associated rules. This action cannot be undone.

**Method:** `DELETE`
**URL:** `/calendars/schedules/:id`
**Scope:** `calendars.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string | Yes | The schedule ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/calendars/schedules/SCHEDULE_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns HTTP status `200 OK` on successful deletion. Returns `404` if schedule not found.

> **Warning:** This permanently removes the schedule and all its rules. This action cannot be undone.
