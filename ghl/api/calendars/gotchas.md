# Calendars API — Webhooks, Errors & Gotchas

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

Cross-cutting reference material: webhooks, error responses, status-code quirks, best practices, and references. See `core.md` for authentication, scopes, and rate limits.

---

## Webhooks

The following webhook events are available for calendar/appointment changes:

### Available Events

| Event | Trigger |
|-------|---------|
| `AppointmentCreate` | When an appointment is created |
| `AppointmentUpdate` | When an appointment is updated |
| `AppointmentDelete` | When an appointment is deleted |

### Webhook Payload Example

```json
{
  "type": "AppointmentCreate",
  "locationId": "location_id",
  "appointment": {
    "id": "appointment_id",
    "calendarId": "calendar_id",
    "contactId": "contact_id",
    "groupId": "group_id",
    "title": "Consultation Call",
    "appointmentStatus": "confirmed",
    "assignedUserId": "user_id",
    "users": ["user_id"],
    "address": "https://zoom.us/j/123456",
    "notes": "Initial consultation",
    "source": "booking_widget",
    "startTime": "2026-03-15T10:00:00-04:00",
    "endTime": "2026-03-15T10:30:00-04:00",
    "dateAdded": "2026-03-10T14:30:00Z",
    "dateUpdated": "2026-03-10T14:30:00Z"
  }
}
```

---

## Error Responses

| Status Code | Description |
|-------------|-------------|
| `400` | Bad Request - Invalid parameters |
| `401` | Unauthorized - Invalid or missing token |
| `403` | Forbidden - Insufficient scope |
| `404` | Not Found - Resource doesn't exist |
| `422` | Unprocessable Entity - Validation error |
| `429` | Too Many Requests - Rate limit exceeded |

### Error Response Format

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Description of the error"
  }
}
```

---

## Status Code Quirks

Some calendar endpoints return non-standard success codes. Account for these when checking responses:

- **Delete Event** (`DELETE /calendars/events/:eventId`) returns `201` on success, not `200`/`204`.
- **Create Block Slot** returns `201 Created`; **Update Block Slot** also returns `201` (not `200`).
- **Create Appointment** returns `200 OK` (not `201`), unlike most create endpoints.
- **Get Event Calendar Availability Schedule** returns `404` if the calendar/schedule is not found.
- Schedule create/update return `422` on validation errors.

---

## Best Practices

### Timezone Handling

- Always specify timezone when creating appointments and checking availability
- Use IANA timezone names (e.g., `America/New_York`, not `EST`)
- The `startTime` format should match the `timezone` or `selectedTimezone`

### Appointment Management

- Always obtain a valid `contactId` before creating appointments
- Use the Get Free Slots endpoint before creating appointments to ensure slot availability
- Set `toNotify: true` to send automatic notifications to contacts

### Slot Configuration

- `slotDuration` controls the length of each bookable slot
- `slotBuffer` adds buffer time between back-to-back slots
- `slotInterval` controls how frequently slots are offered

---

## References

- [HighLevel API Documentation](https://marketplace.gohighlevel.com/docs/)
- [Calendars API Section](https://marketplace.gohighlevel.com/docs/ghl/calendars/calendars-api/index.html)
- [Calendar Events](https://marketplace.gohighlevel.com/docs/ghl/calendars/calendar-events/index.html)
- [OAuth Scopes Reference](https://marketplace.gohighlevel.com/docs/Authorization/Scopes/index.html)
- [GitHub API Docs Repository](https://github.com/GoHighLevel/highlevel-api-docs)
- [HighLevel Developer Community](https://developers.gohighlevel.com/)
