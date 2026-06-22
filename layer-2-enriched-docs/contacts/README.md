# Contacts API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

The Contacts API manages contacts (leads, customers, prospects) in GoHighLevel — the foundation of CRM operations. Contacts can be associated with opportunities, appointments, workflows, campaigns, notes, tasks, and tags.

**Required scopes:** `contacts.readonly` (read), `contacts.write` (read/write).

## Files

| File | Endpoints / Topics |
|------|--------------------|
| [crud.md](./crud.md) | Overview, auth, rate limits, full Contact object schema, DND settings. Create / Get / Update / Delete / Upsert Contact, Get Contacts by Business |
| [search.md](./search.md) | Search Contacts (`POST /contacts/search`) — filters, sorting, pagination (`pageLimit` gotcha) |
| [tags.md](./tags.md) | Add Tags, Remove Tags, Bulk Update Tags (`contacts` vs `contactIds` gotcha) |
| [notes-tasks.md](./notes-tasks.md) | Notes CRUD, Tasks CRUD, Update Task Status, Get Appointments |
| [campaigns-workflows.md](./campaigns-workflows.md) | Add/Remove from Campaign, Remove from All Campaigns, Add/Remove from Workflow |
| [webhooks-errors.md](./webhooks-errors.md) | Webhook events & payloads, error codes, gotchas, best practices, references |
