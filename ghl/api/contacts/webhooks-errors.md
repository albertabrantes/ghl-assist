# Contacts API — Webhooks, Errors & Best Practices

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Webhooks

The Contacts API supports webhooks for real-time notifications.

### Available Events

| Event | Trigger |
|-------|---------|
| `ContactCreate` | New contact created |
| `ContactUpdate` | Contact information updated |
| `ContactDelete` | Contact deleted |
| `ContactTagUpdate` | Tags added or removed |
| `ContactDndUpdate` | DND settings changed |
| `NoteCreate` | Note created for a contact |
| `NoteUpdate` | Note updated |
| `NoteDelete` | Note deleted |
| `TaskCreate` | Task created for a contact |
| `TaskUpdate` | Task updated |
| `TaskComplete` | Task marked as complete |
| `TaskDelete` | Task deleted |

### Webhook Payload Example (ContactCreate)

```json
{
  "event": "ContactCreate",
  "locationId": "loc_xyz789",
  "contact": {
    "id": "contact_new123",
    "firstName": "Jane",
    "lastName": "Doe",
    "email": "jane.doe@example.com",
    "phone": "+14155551234",
    "tags": ["lead", "webinar"],
    "createdAt": "2026-02-04T15:30:00Z"
  },
  "timestamp": "2026-02-04T15:30:00Z"
}
```

### Webhook Payload Example (ContactTagUpdate)

```json
{
  "event": "ContactTagUpdate",
  "locationId": "loc_xyz789",
  "contact": {
    "id": "contact_abc123",
    "tagsAdded": ["high-priority", "q1-target"],
    "tagsRemoved": ["low-priority"],
    "currentTags": ["customer", "vip", "high-priority", "q1-target"]
  },
  "timestamp": "2026-02-04T16:45:00Z"
}
```

### Webhook Payload Example (ContactDndUpdate)

```json
{
  "event": "ContactDndUpdate",
  "locationId": "loc_xyz789",
  "contact": {
    "id": "contact_abc123",
    "dnd": true,
    "dndSettings": {
      "Email": {
        "status": "active",
        "message": "User unsubscribed",
        "code": "UNSUBSCRIBE"
      }
    }
  },
  "timestamp": "2026-02-04T17:00:00Z"
}
```

### Webhook Payload Example (TaskCreate)

```json
{
  "event": "TaskCreate",
  "locationId": "loc_xyz789",
  "contactId": "contact_abc123",
  "task": {
    "id": "task_001",
    "title": "Follow up with client",
    "assignedTo": "user_456",
    "dueDate": "2026-02-10T14:00:00Z",
    "completed": false
  },
  "timestamp": "2026-02-04T10:30:00Z"
}
```

---

## Error Responses

| Status Code | Description |
|-------------|-------------|
| `400` | Bad Request - Invalid parameters |
| `401` | Unauthorized - Invalid or missing token |
| `403` | Forbidden - Insufficient scope |
| `404` | Not Found - Contact/resource doesn't exist |
| `409` | Conflict - Duplicate contact (email/phone already exists) |
| `422` | Unprocessable Entity - Validation error |
| `429` | Too Many Requests - Rate limit exceeded |

### Error Response Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email format",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address"
      }
    ]
  }
}
```

### Common Error Codes

| Code | Description |
|------|-------------|
| `VALIDATION_ERROR` | Request validation failed |
| `CONTACT_NOT_FOUND` | Contact ID doesn't exist |
| `DUPLICATE_CONTACT` | Contact with email/phone already exists |
| `INVALID_LOCATION` | Location ID is invalid |
| `INVALID_TAG` | Tag format is invalid |
| `INSUFFICIENT_PERMISSIONS` | Missing required scope |
| `RATE_LIMIT_EXCEEDED` | Too many requests |

---

## Gotchas & Best Practices

### Known Gotchas

- **Search uses `pageLimit`, not `limit`.** On `POST /contacts/search`, the page-size field is `pageLimit`. Using `limit` returns a 422 error. (See [search.md](./search.md).)
- **Bulk tag update uses `contacts`, not `contactIds`.** On `POST /contacts/bulk/tags/update/{type}`, the array of contact IDs must be named `contacts`. Using `contactIds` returns a 422 error. `locationId` is also required — omitting it returns 400 "LocationId can't be undefined". (See [tags.md](./tags.md).)
- **Tags are case-sensitive.** Duplicate tags are automatically ignored on add.

### Contact Deduplication

- Use the [Upsert Contact](./crud.md#upsert-contact) endpoint to prevent duplicates.
- Match on `email` (primary) or `phone` (secondary).
- Normalize phone numbers to E.164 format before upserting.

### Tag Management

- Use consistent tag naming conventions (lowercase, hyphenated).
- Avoid creating duplicate tags with different cases.
- Use bulk operations for tagging multiple contacts.
- Regularly audit and clean up unused tags.

### Performance Optimization

- Use pagination for large result sets (limit 100 max).
- Filter searches by location and specific criteria.
- Cache location and pipeline data that doesn't change frequently.
- Use webhooks instead of polling for real-time updates.

### DND Settings

- Always check DND status before sending communications.
- Respect channel-specific DND settings.
- Update DND settings when contacts opt-out.
- Store opt-out reasons for compliance.

### Custom Fields

- Query location custom field definitions before using.
- Use custom field IDs (not keys) in API calls.
- Validate custom field values match their type.
- Document custom field usage for your integration.

---

## References

- [HighLevel API Documentation](https://marketplace.gohighlevel.com/docs/)
- [OAuth Scopes Reference](https://marketplace.gohighlevel.com/docs/Authorization/Scopes/index.html)
- [GitHub API Docs Repository](https://github.com/GoHighLevel/highlevel-api-docs)
- [Contacts API Interactive Docs](https://highlevel.stoplight.io/docs/integrations/contacts)
