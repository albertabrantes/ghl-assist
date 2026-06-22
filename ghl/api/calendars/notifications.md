# Calendars API — Calendar Notifications

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Notification settings per calendar. See `core.md` for authentication, scopes, and rate limits.

---

## Get Notifications

Get calendar notifications based on query parameters.

**Method:** `GET`
**URL:** `/calendars/:calendarId/notifications`
**Scope:** `calendars.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `calendarId` | string | Yes | The calendar ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/calendars/CALENDAR_ID/notifications' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

Returns notification settings with HTTP status `200 OK`.

---

## Update Notification

Update a specific event notification by ID.

**Method:** `PUT`
**URL:** `/calendars/:calendarId/notifications/:notificationId`
**Scope:** `calendars.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `calendarId` | string | Yes | The calendar ID |
| `notificationId` | string | Yes | The notification ID |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/calendars/CALENDAR_ID/notifications/NOTIFICATION_ID' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "enabled": true
  }'
```

### Response

Returns the updated notification with HTTP status `200 OK`.
