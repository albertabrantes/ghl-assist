# Calendars API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

The Calendars API manages calendars, appointments, events, blocked slots, calendar groups, calendar resources (rooms & equipment), appointment notes, notifications, and availability schedules within GoHighLevel. Calendars are used for booking appointments with contacts and managing team availability. This reference is split into topic files; start with `core.md` for the shared overview, authentication, scopes, rate limits, and the common object/type schemas.

## Files

| File | Endpoints / Topics Covered |
|------|----------------------------|
| [`core.md`](./core.md) | Overview, authentication, required scopes, rate limits, calendar types, the Appointment object schema, full endpoints summary, and calendar CRUD: Get Calendars, Get Calendar, Create/Update/Delete Calendar, Get Free Slots |
| [`events-appointments.md`](./events-appointments.md) | Appointments (Create/Get/Update), Get Calendar Events, Delete Event, blocked slots (Get Blocked Slots, Create/Update Block Slot), and appointment notes (Get/Create/Update/Delete) |
| [`groups.md`](./groups.md) | Calendar groups: Create Calendar Group, Validate Group Slug, Delete Calendar Group |
| [`resources.md`](./resources.md) | Calendar resources (rooms & equipment, deprecated Services V1): List/Get/Create/Update/Delete Calendar Resource |
| [`availability.md`](./availability.md) | User availability schedules: List/Search, Get by ID, Get Event Calendar schedule, Create/Update/Delete schedule |
| [`notifications.md`](./notifications.md) | Calendar notifications: Get Notifications, Update Notification |
| [`gotchas.md`](./gotchas.md) | Webhooks (Appointment Create/Update/Delete), error responses, non-standard status-code quirks, best practices (timezones, appointments, slots), references |
