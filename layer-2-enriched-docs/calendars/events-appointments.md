# Calendars API — Events, Appointments & Notes

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Covers calendar events, appointments, blocked slots, and appointment notes. See `core.md` for authentication, scopes, rate limits, and the shared Appointment Object schema.

---

# Calendar Events / Appointments

## Create Appointment

Create a new appointment on a calendar.

**Method:** `POST`
**URL:** `/calendars/events/appointments`
**Scope:** `calendars/events.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `calendarId` | string | Yes | Calendar ID to book on |
| `contactId` | string | Yes | Contact ID to associate with the appointment |
| `startTime` | string | Yes | Start time in ISO 8601 format (`YYYY-MM-DDTHH:mm:ss`) in the `selectedTimezone` |
| `endTime` | string | Yes | End time in ISO 8601 format |
| `title` | string | No | Title/name of the appointment |
| `appointmentStatus` | string | No | Status: `confirmed`, `cancelled`, `showed`, `noshow`, `invalid` |
| `assignedUserId` | string | No | User ID assigned to the appointment |
| `address` | string | No | Meeting address or link |
| `toNotify` | boolean | No | Whether to send notifications |
| `notes` | string | No | Additional notes |
| `timezone` | string | No | Timezone for the appointment (e.g., `America/New_York`) |

> **Important:** A `contactId` is required. You must first retrieve an existing contact (via Get Contact) or create a new one (via Create Contact) before creating an appointment.

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/calendars/events/appointments' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "calendarId": "calendar_id",
    "contactId": "contact_id",
    "startTime": "2026-03-15T10:00:00",
    "endTime": "2026-03-15T10:30:00",
    "title": "Consultation Call",
    "appointmentStatus": "confirmed",
    "assignedUserId": "user_id",
    "timezone": "America/New_York",
    "toNotify": true
  }'
```

### Response

Returns the created appointment object with HTTP status `200 OK`.

---

## Get Appointment

Get a single appointment by its event ID.

**Method:** `GET`
**URL:** `/calendars/events/appointments/:eventId`
**Scope:** `calendars/events.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `eventId` | string | Yes | The appointment event ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/events/appointments/EVENT_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns the appointment object with HTTP status `200 OK`.

---

## Update Appointment

Update an existing appointment by event ID.

**Method:** `PUT`
**URL:** `/calendars/events/appointments/:eventId`
**Scope:** `calendars/events.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `eventId` | string | Yes | The appointment event ID |

### Request Body

Same fields as [Create Appointment](#create-appointment). Only provided fields will be updated.

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/calendars/events/appointments/EVENT_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "appointmentStatus": "confirmed",
    "startTime": "2026-03-15T11:00:00",
    "endTime": "2026-03-15T11:30:00"
  }'
```

### Response

Returns the updated appointment object with HTTP status `200 OK`.

---

## Get Calendar Events

Get calendar events for a location, optionally filtered by calendar and date range.

**Method:** `GET`
**URL:** `/calendars/events`
**Scope:** `calendars/events.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `calendarId` | string | No | Filter by specific calendar |
| `startTime` | string | No | Filter start time |
| `endTime` | string | No | Filter end time |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/events?locationId=LOCATION_ID&calendarId=CALENDAR_ID&startTime=2026-03-01&endTime=2026-03-31' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns a list of calendar events with HTTP status `200 OK`.

---

## Delete Event

Delete a calendar event (appointment or block slot) by its event ID.

**Method:** `DELETE`
**URL:** `/calendars/events/:eventId`
**Scope:** `calendars/events.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `eventId` | string | Yes | The event ID to delete |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/calendars/events/EVENT_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns HTTP status `201` on successful deletion.

> **Warning:** This is a destructive operation. Deleting an event cannot be undone.

---

## Get Blocked Slots

Get blocked time slots for calendars.

**Method:** `GET`
**URL:** `/calendars/blocked-slots`
**Scope:** `calendars/events.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `calendarId` | string | No | Filter by specific calendar |
| `startTime` | string | No | Filter start time |
| `endTime` | string | No | Filter end time |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/blocked-slots?locationId=LOCATION_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns a list of blocked slots with HTTP status `200 OK`.

---

## Create Block Slot

Create a new blocked time slot on a calendar.

**Method:** `POST`
**URL:** `/calendars/events/block-slots`
**Scope:** `calendars/events.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `calendarId` | string | Yes | Calendar ID to block time on |
| `startTime` | string | Yes | Block start time (ISO 8601) |
| `endTime` | string | Yes | Block end time (ISO 8601) |
| `title` | string | No | Title for the blocked slot |
| `assignedUserId` | string | No | User ID this block applies to |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/calendars/events/block-slots' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "calendarId": "calendar_id",
    "startTime": "2026-03-15T12:00:00",
    "endTime": "2026-03-15T13:00:00",
    "title": "Lunch Break"
  }'
```

### Response

Returns the created block slot with HTTP status `201 Created`.

---

## Update Block Slot

Update an existing block slot by event ID.

**Method:** `PUT`
**URL:** `/calendars/events/block-slots/:eventId`
**Scope:** `calendars/events.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `eventId` | string | Yes | The block slot event ID |

### Request Body

Same fields as [Create Block Slot](#create-block-slot).

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/calendars/events/block-slots/EVENT_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "startTime": "2026-03-15T12:00:00",
    "endTime": "2026-03-15T14:00:00"
  }'
```

### Response

Returns the updated block slot with HTTP status `201`.

---

# Appointment Notes

## Get Appointment Notes

Get all notes associated with a specific appointment.

**Method:** `GET`
**URL:** `/calendars/appointments/:appointmentId/notes`
**Scope:** `calendars/events.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `appointmentId` | string | Yes | The appointment ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/appointments/APPOINTMENT_ID/notes' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns a list of notes with HTTP status `200 OK`.

> **Note:** Appointment notes are internal to GoHighLevel and are not synced to external calendars (e.g., Google Calendar). Notes can be created by users, workflows, or the API. You can filter notes by source: User, API, and/or Workflow.

---

## Create Appointment Note

Create a new note on an appointment.

**Method:** `POST`
**URL:** `/calendars/appointments/:appointmentId/notes`
**Scope:** `calendars/events.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `appointmentId` | string | Yes | The appointment ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `body` | string | Yes | The note content/text |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/calendars/appointments/APPOINTMENT_ID/notes' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "body": "Follow up with client regarding proposal."
  }'
```

### Response

Returns the created note with HTTP status `201 Created`.

---

## Update Appointment Note

Update an existing appointment note.

**Method:** `PUT`
**URL:** `/calendars/appointments/:appointmentId/notes/:noteId`
**Scope:** `calendars/events.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `appointmentId` | string | Yes | The appointment ID |
| `noteId` | string | Yes | The note ID to update |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `body` | string | Yes | Updated note content |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/calendars/appointments/APPOINTMENT_ID/notes/NOTE_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "body": "Updated: Client confirmed the proposal."
  }'
```

### Response

Returns the updated note with HTTP status `200 OK`.

---

## Delete Appointment Note

Remove a note from an appointment.

**Method:** `DELETE`
**URL:** `/calendars/appointments/:appointmentId/notes/:noteId`
**Scope:** `calendars/events.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `appointmentId` | string | Yes | The appointment ID |
| `noteId` | string | Yes | The note ID to delete |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/calendars/appointments/APPOINTMENT_ID/notes/NOTE_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns HTTP status `200 OK` on successful deletion.
