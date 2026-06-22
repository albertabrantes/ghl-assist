# Contacts API — Notes, Tasks & Appointments

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Get All Notes

Retrieve all notes associated with a contact.

**Method:** `GET`
**URL:** `/contacts/{contactId}/notes`
**Scope:** `contacts.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/notes' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "notes": [
    {
      "id": "note_001",
      "contactId": "contact_abc123",
      "body": "Initial discovery call completed. Client interested in enterprise package.",
      "userId": "user_456",
      "userName": "Sarah Johnson",
      "createdAt": "2026-02-01T10:30:00Z",
      "updatedAt": "2026-02-01T10:30:00Z"
    },
    {
      "id": "note_002",
      "contactId": "contact_abc123",
      "body": "Follow-up scheduled for next week.",
      "userId": "user_456",
      "userName": "Sarah Johnson",
      "createdAt": "2026-02-03T14:15:00Z",
      "updatedAt": "2026-02-03T14:15:00Z"
    }
  ]
}
```

---

## Get Note

Retrieve a single note by ID.

**Method:** `GET`
**URL:** `/contacts/{contactId}/notes/{id}`
**Scope:** `contacts.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |
| `id` | string | Yes | The note ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/notes/note_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "note": {
    "id": "note_001",
    "contactId": "contact_abc123",
    "body": "Initial discovery call completed. Client interested in enterprise package.",
    "userId": "user_456",
    "userName": "Sarah Johnson",
    "createdAt": "2026-02-01T10:30:00Z",
    "updatedAt": "2026-02-01T10:30:00Z"
  }
}
```

---

## Create Note

Create a new note for a contact.

**Method:** `POST`
**URL:** `/contacts/{contactId}/notes`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `body` | string | Yes | Note content (markdown supported) |
| `userId` | string | No | User creating the note (defaults to authenticated user) |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/notes' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "body": "Client requested additional information about pricing tiers."
  }'
```

### Response

Returns the created note object with HTTP status `201 Created`.

---

## Update Note

Update an existing note.

**Method:** `PUT`
**URL:** `/contacts/{contactId}/notes/{id}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |
| `id` | string | Yes | The note ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `body` | string | Yes | Updated note content |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/notes/note_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "body": "Initial discovery call completed. Client interested in enterprise package. UPDATE: Sent proposal via email."
  }'
```

### Response

Returns the updated note object with HTTP status `200 OK`.

---

## Delete Note

Delete a note permanently.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}/notes/{id}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |
| `id` | string | Yes | The note ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/notes/note_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with confirmation message.

---

## Get All Tasks

Retrieve all tasks associated with a contact.

**Method:** `GET`
**URL:** `/contacts/{contactId}/tasks`
**Scope:** `contacts.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/tasks' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "tasks": [
    {
      "id": "task_001",
      "contactId": "contact_abc123",
      "title": "Send follow-up email",
      "body": "Send pricing information and case studies",
      "assignedTo": "user_456",
      "dueDate": "2026-02-10T17:00:00Z",
      "completed": false,
      "completedAt": null,
      "createdAt": "2026-02-04T10:00:00Z",
      "updatedAt": "2026-02-04T10:00:00Z"
    },
    {
      "id": "task_002",
      "contactId": "contact_abc123",
      "title": "Schedule demo call",
      "body": "Schedule product demo for next week",
      "assignedTo": "user_456",
      "dueDate": "2026-02-08T15:00:00Z",
      "completed": true,
      "completedAt": "2026-02-05T14:30:00Z",
      "createdAt": "2026-02-03T09:00:00Z",
      "updatedAt": "2026-02-05T14:30:00Z"
    }
  ]
}
```

---

## Get Task

Retrieve a single task by ID.

**Method:** `GET`
**URL:** `/contacts/{contactId}/tasks/{taskId}`
**Scope:** `contacts.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |
| `taskId` | string | Yes | The task ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/tasks/task_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "task": {
    "id": "task_001",
    "contactId": "contact_abc123",
    "title": "Send follow-up email",
    "body": "Send pricing information and case studies",
    "assignedTo": "user_456",
    "dueDate": "2026-02-10T17:00:00Z",
    "completed": false,
    "completedAt": null,
    "createdAt": "2026-02-04T10:00:00Z",
    "updatedAt": "2026-02-04T10:00:00Z"
  }
}
```

---

## Create Task

Create a new task for a contact.

**Method:** `POST`
**URL:** `/contacts/{contactId}/tasks`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | Yes | Task title/summary |
| `body` | string | No | Detailed task description |
| `assignedTo` | string | No | User ID to assign task to |
| `dueDate` | string | No | ISO 8601 due date/time |
| `completed` | boolean | No | Task completion status (default: false) |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/tasks' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "title": "Call to discuss contract terms",
    "body": "Review pricing, timeline, and deliverables",
    "assignedTo": "user_456",
    "dueDate": "2026-02-12T14:00:00Z"
  }'
```

### Response

Returns the created task object with HTTP status `201 Created`.

---

## Update Task

Update an existing task.

**Method:** `PUT`
**URL:** `/contacts/{contactId}/tasks/{taskId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |
| `taskId` | string | Yes | The task ID |

### Request Body

All fields are optional. Only include fields you want to update.

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Task title |
| `body` | string | Task description |
| `assignedTo` | string | Assigned user ID |
| `dueDate` | string | ISO 8601 due date/time |
| `completed` | boolean | Completion status |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/tasks/task_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "title": "URGENT: Send follow-up email",
    "dueDate": "2026-02-08T17:00:00Z"
  }'
```

### Response

Returns the updated task object with HTTP status `200 OK`.

---

## Update Task Status

Mark a task as complete or incomplete.

**Method:** `PUT`
**URL:** `/contacts/{contactId}/tasks/{taskId}/completed`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |
| `taskId` | string | Yes | The task ID |

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `completed` | boolean | Yes | Completion status (true/false) |

### Request Example

```bash
curl -X PUT \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/tasks/task_001/completed' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "completed": true
  }'
```

### Response

Returns the updated task object with `completedAt` timestamp set (if marking as complete) and HTTP status `200 OK`.

---

## Delete Task

Delete a task permanently.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}/tasks/{taskId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |
| `taskId` | string | Yes | The task ID |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/tasks/task_001' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28'
```

### Response

HTTP status `200 OK` with confirmation message.

---

## Get Appointments

Retrieve all appointments associated with a contact.

**Method:** `GET`
**URL:** `/contacts/{contactId}/appointments`
**Scope:** `contacts.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | The contact ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `startDate` | string | No | Filter appointments from this date (ISO 8601) |
| `endDate` | string | No | Filter appointments until this date (ISO 8601) |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/contacts/contact_abc123/appointments?startDate=2026-02-01T00:00:00Z&endDate=2026-02-28T23:59:59Z' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Content-Type: application/json'
```

### Response Example

```json
{
  "appointments": [
    {
      "id": "appt_001",
      "contactId": "contact_abc123",
      "calendarId": "cal_789",
      "title": "Product Demo",
      "startTime": "2026-02-10T14:00:00Z",
      "endTime": "2026-02-10T15:00:00Z",
      "status": "confirmed",
      "assignedTo": "user_456",
      "meetingLocation": "https://zoom.us/j/123456789",
      "notes": "Demo of enterprise features",
      "createdAt": "2026-02-04T10:00:00Z"
    }
  ]
}
```

> **Note:** This endpoint returns appointments only. To create or manage appointments, use the Calendars API.
