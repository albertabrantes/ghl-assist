# Locations (Sub-Accounts) API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

The Locations API — officially the **Sub-Account API** — manages sub-accounts within a GoHighLevel agency and their associated resources: custom fields, custom values, tags, templates, tasks, recurring tasks, and timezones.

**Access model:** Cross-location endpoints (Search, Create, Delete) require an **Agency-level** token. Within-location endpoints accept a **Sub-Account-level** token. Get/Update Location accept either.

Most endpoints here are 🔬 **Unverified** — documented from the OpenAPI spec and official docs, not confirmed against a live API.

## Files

| File | Endpoints / Topics |
|------|--------------------|
| [core.md](./core.md) | Overview, scopes, auth, rate limits, full Location object schema (settings/social/business). Search Locations, Get / Create / Update / Delete Location |
| [custom-fields.md](./custom-fields.md) | Location-level custom fields CRUD + file upload, `dataType` enum (incl. `MONETORY`) |
| [custom-values.md](./custom-values.md) | Custom values CRUD |
| [tags-templates.md](./tags-templates.md) | Tags CRUD; Get/Delete Templates (no create endpoint; delete uses readonly scope) |
| [tasks.md](./tasks.md) | Task Search, Recurring Tasks CRUD, Fetch Timezones |
| [gotchas.md](./gotchas.md) | Error codes; plan/scope quirks, `MONETORY`, Custom Fields V2, version header, search/twilio notes |
