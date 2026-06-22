# Calendars API — Core

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Calendars API allows you to manage calendars, appointments, events, blocked slots, calendar groups, calendar resources (rooms & equipment), appointment notes, notifications, and availability schedules within GoHighLevel. Calendars are used for booking appointments with contacts and managing team availability.

The Calendars API is organized into the following subsections:
- **Calendars** - Core calendar CRUD and free slot lookup
- **Calendar Events** - Appointments, blocked slots, and event deletion
- **Calendar Groups** - Grouping calendars together with shared booking pages
- **Calendar Resources** - Rooms & equipment management (deprecated, Services V1)
- **Calendar Notifications** - Notification settings per calendar
- **Appointment Notes** - Notes attached to appointments
- **Availability / Schedules** - User and calendar availability management

This documentation is split across the following files:
- `core.md` — overview, auth, scopes, rate limits, shared object/type tables, calendar CRUD, free slots
- `events-appointments.md` — appointments, calendar events, blocked slots, appointment notes
- `groups.md` — calendar groups
- `resources.md` — calendar resources (rooms & equipment)
- `availability.md` — availability schedules
- `notifications.md` — calendar notifications
- `gotchas.md` — webhooks, error responses, best practices, references

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `calendars.readonly` | Read | View calendars |
| `calendars.write` | Read/Write | Create, update, delete calendars |
| `calendars/events.readonly` | Read | View appointments and events |
| `calendars/events.write` | Read/Write | Create, update, delete appointments and events |
| `calendars/groups.readonly` | Read | View calendar groups |
| `calendars/groups.write` | Read/Write | Create, update, delete calendar groups |
| `calendars/resources.readonly` | Read | View calendar resources |
| `calendars/resources.write` | Read/Write | Create, update, delete calendar resources |

---

## Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| **CALENDARS** (this file) |
| Get Calendars | GET | calendars.readonly | Get all calendars in a location |
| Get Calendar | GET | calendars.readonly | Get calendar by ID |
| Create Calendar | POST | calendars.write | Create calendar in a location |
| Update Calendar | PUT | calendars.write | Update calendar by ID |
| Delete Calendar | DELETE | calendars.write | Delete calendar by ID |
| Get Free Slots | GET | calendars.readonly | Get free slots for a calendar between a date range |
| **CALENDAR EVENTS / APPOINTMENTS** (`events-appointments.md`) |
| Create Appointment | POST | calendars/events.write | Create an appointment |
| Get Appointment | GET | calendars/events.readonly | Get appointment by ID |
| Update Appointment | PUT | calendars/events.write | Update appointment by ID |
| Get Calendar Events | GET | calendars/events.readonly | Get calendar events |
| Delete Event | DELETE | calendars/events.write | Delete event by ID |
| Get Blocked Slots | GET | calendars/events.readonly | Get blocked slots |
| Create Block Slot | POST | calendars/events.write | Create a block slot |
| Update Block Slot | PUT | calendars/events.write | Update block slot by ID |
| Get Appointment Notes | GET | calendars/events.readonly | Get notes for an appointment |
| Create Appointment Note | POST | calendars/events.write | Create a note on an appointment |
| Update Appointment Note | PUT | calendars/events.write | Update an appointment note |
| Delete Appointment Note | DELETE | calendars/events.write | Delete an appointment note |
| **CALENDAR GROUPS** (`groups.md`) |
| Create Calendar Group | POST | calendars/groups.write | Create calendar group |
| Validate Group Slug | POST | calendars/groups.write | Validate if group slug is available |
| Delete Calendar Group | DELETE | calendars/groups.write | Delete calendar group |
| **CALENDAR RESOURCES** (`resources.md`, Deprecated - Services V1) |
| List Calendar Resources | GET | calendars/resources.readonly | List resources by type and location |
| Get Calendar Resource | GET | calendars/resources.readonly | Get resource by ID |
| Create Calendar Resource | POST | calendars/resources.write | Create calendar resource |
| Update Calendar Resource | PUT | calendars/resources.write | Update calendar resource |
| Delete Calendar Resource | DELETE | calendars/resources.write | Delete calendar resource |
| **CALENDAR NOTIFICATIONS** (`notifications.md`) |
| Get Notifications | GET | calendars.readonly | Get calendar notifications |
| Update Notification | PUT | calendars.write | Update notification by ID |
| **AVAILABILITY / SCHEDULES** (`availability.md`) |
| List User Availability Schedules | GET | calendars.readonly | Search/list user availability schedules |
| Get User Availability Schedule | GET | calendars.readonly | Get schedule by ID |
| Get Event Calendar Availability Schedule | GET | calendars.readonly | Get availability schedule for an event calendar |
| Create User Availability Schedule | POST | calendars.write | Create new availability schedule |
| Update User Availability Schedule | PUT | calendars.write | Update an availability schedule |
| Delete User Availability Schedule | DELETE | calendars.write | Permanently delete a schedule |

---

## Authentication

All requests require authentication via one of:

**OAuth 2.0 Bearer Token:**
```
Authorization: Bearer {access_token}
```

**Private Integration Token:**
```
Authorization: Bearer {private_token}
```

---

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

**Rate Limit Headers:**
- `X-RateLimit-Limit-Daily` - Total daily limit
- `X-RateLimit-Daily-Remaining` - Remaining daily requests
- `X-RateLimit-Interval-Milliseconds` - Burst interval window
- `X-RateLimit-Max` - Max requests in burst interval
- `X-RateLimit-Remaining` - Remaining burst requests

When exceeded: HTTP `429 Too Many Requests` with `Retry-After` header.

---

## Calendar Types

When creating or updating calendars, the `calendarType` field accepts the following values:

| Type | Description |
|------|-------------|
| `RoundRobin_OptimizeForAvailability` | Round robin optimized for availability |
| `RoundRobin_OptimizeForEqualDistribution` | Round robin optimized for equal distribution |
| `EventCalendar` | Standard event calendar |
| `ClassBooking` | Class/group booking calendar |
| `CollectiveBooking` | Collective booking (all team members must be free) |
| `ServiceBooking` | Service booking calendar |

---

## Appointment Object

The appointment object contains the following fields (returned from GET endpoints and webhook payloads):

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique appointment identifier |
| `calendarId` | string | Associated calendar ID |
| `contactId` | string | Linked contact ID |
| `groupId` | string | Calendar group identifier |
| `title` | string | Appointment title |
| `appointmentStatus` | string | Status (e.g., `confirmed`, `cancelled`, `showed`, `noshow`, `invalid`) |
| `assignedUserId` | string | Primary assigned user ID |
| `users` | array | List of assigned user IDs |
| `address` | string | Meeting location or URL |
| `notes` | string | Appointment notes |
| `source` | string | Creation source (e.g., `booking_widget`, `api`) |
| `startTime` | string | ISO 8601 start datetime |
| `endTime` | string | ISO 8601 end datetime |
| `dateAdded` | string | Creation timestamp |
| `dateUpdated` | string | Last modification timestamp |

---

# Calendars

## Get Calendars

Retrieve all calendars associated with a specific location.

**Method:** `GET`
**URL:** `/calendars/`
**Scope:** `calendars.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/?locationId=YOUR_LOCATION_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns a list of calendars with HTTP status `200 OK`.

---

## Get Calendar

Get a single calendar by its ID.

**Method:** `GET`
**URL:** `/calendars/:calendarId`
**Scope:** `calendars.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `calendarId` | string | Yes | The calendar ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/CALENDAR_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns the calendar object with HTTP status `200 OK`.

---

## Create Calendar

Create a new calendar in a location.

**Method:** `POST`
**URL:** `/calendars/`
**Scope:** `calendars.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `locationId` | string | Yes | The location/sub-account ID |
| `name` | string | Yes | Name of the calendar |
| `calendarType` | string | No | Type of calendar (see [Calendar Types](#calendar-types)) |
| `description` | string | No | Description of the calendar |
| `slug` | string | No | URL slug for the calendar |
| `widgetSlug` | string | No | Calendar widget slug |
| `widgetType` | string | No | Widget style (e.g., `classic`, `neo`) |
| `teamMembers` | array | No | Array of team member objects with user IDs and priorities |
| `openHours` | array | No | Availability/open hours configuration |
| `formId` | string | No | ID of the associated booking form |
| `notifications` | object | No | Notification settings for this calendar |
| `slotDuration` | number | No | Duration of each appointment slot (minutes) |
| `slotBuffer` | number | No | Buffer time between slots (minutes) |
| `slotInterval` | number | No | Interval between available slots (minutes) |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/calendars/' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "locationId": "location_id",
    "name": "Consultation Calendar",
    "calendarType": "RoundRobin_OptimizeForAvailability",
    "description": "30-minute consultation slots",
    "slotDuration": 30,
    "slotBuffer": 10,
    "teamMembers": [
      {
        "userId": "user_id_1",
        "priority": 1
      }
    ]
  }'
```

### Response

Returns the created calendar object with HTTP status `200 OK`.

---

## Update Calendar

Update an existing calendar by ID.

**Method:** `PUT`
**URL:** `/calendars/:calendarId`
**Scope:** `calendars.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `calendarId` | string | Yes | The calendar ID to update |

### Request Body

Same fields as [Create Calendar](#create-calendar). Only provided fields will be updated.

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/calendars/CALENDAR_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Updated Calendar Name",
    "slotDuration": 45
  }'
```

### Response

Returns the updated calendar object with HTTP status `200 OK`.

---

## Delete Calendar

Delete a calendar by ID.

**Method:** `DELETE`
**URL:** `/calendars/:calendarId`
**Scope:** `calendars.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `calendarId` | string | Yes | The calendar ID to delete |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/calendars/CALENDAR_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns HTTP status `200 OK` on successful deletion.

> **Warning:** This is a destructive operation. Deleting a calendar will remove all associated appointments and configurations. This action cannot be undone.

---

## Get Free Slots

Get free/available slots for a calendar between a date range. Optionally request free slots in a particular timezone and for a particular user.

**Method:** `GET`
**URL:** `/calendars/:calendarId/free-slots`
**Scope:** `calendars.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `calendarId` | string | Yes | The calendar ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `startDate` | number | Yes | Start of time range (epoch milliseconds) |
| `endDate` | number | Yes | End of time range (epoch milliseconds) |
| `timezone` | string | No | Timezone (e.g., `America/New_York`) |
| `userId` | string | No | Filter slots for a specific user |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/CALENDAR_ID/free-slots?startDate=1700000000000&endDate=1700600000000&timezone=America/New_York' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns an availability map keyed by date (`YYYY-MM-DD`) with available time slots. HTTP status `200 OK`.
