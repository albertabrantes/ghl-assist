# Invoices API

GoHighLevel Invoices API reference — invoicing, estimates, recurring billing schedules, templates, and Text2Pay. **42 endpoints** across 5 sub-groups, all under the `/invoices/` path prefix.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Files in this folder

| File | Covers | Endpoints |
|------|--------|-----------|
| [schemas.md](./schemas.md) | Overview, required scopes, authentication, rate limits, all object schemas (Invoice, businessDetails, contactDetails, invoiceItems, schedule config/status), webhook events, error responses, general notes | — (reference) |
| [invoice-core.md](./invoice-core.md) | Core invoice lifecycle: generate number, create, list, get, update, delete, void, send, record manual payment, late-fees config, last-visited-at | 11 |
| [templates.md](./templates.md) | Reusable invoice templates: create, list, get, update, delete, late-fees config, payment-methods config | 7 |
| [schedules-text2pay.md](./schedules-text2pay.md) | Recurring invoice schedules (create, list, get, update, delete, update-and-reschedule, activate, manage auto payment, cancel) + Create and Send Text2Pay | 10 |
| [estimates.md](./estimates.md) | Estimates (create, update, delete, generate number, send, create invoice from estimate, list, last-visited-at) + estimate templates (list, create, update, delete, preview) | 13 |

## Quick routing

- **Creating / editing / sending invoices, recording payments** → `invoice-core.md`. The Update Invoice section documents the critical **carry-over PUT pattern** and the `invoiceId`-must-be-mongodb-id and `YYYY-MM-DD` date gotchas.
- **Pre-set invoice templates** → `templates.md`
- **Recurring billing / subscriptions / SMS payment links** → `schedules-text2pay.md`
- **Quotes / proposals / estimate-to-invoice conversion** → `estimates.md`
- **Field names, object shapes, scopes, auth, webhooks, error codes** → `schemas.md`

> **Agent Note:** Documentation created from OpenAPI spec research. Most endpoints are 🔬 unverified until tested against a live GHL API. If you use any endpoint and find errors, missing fields, or wrong schemas, fix the relevant file and update the [master index](../../index.md) — including its Known Issues Log. Add a `> **Tested:**` note to any endpoint you verify with a live API call.
