# Workflows API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Overview

The Workflows API provides **1 read-only endpoint** for listing workflows in a location. Workflows are GHL's **newer** visual automation builder, replacing the older Campaigns for complex automations. They support branching, conditional logic, multiple triggers, multi-channel actions, and custom code.

Two additional contact-level endpoints exist under the Contacts API for adding and removing contacts from workflows. These are cross-referenced here for completeness.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `workflows.readonly` | Read | List workflows in a location |
| `contacts.write` | Read/Write | Add/remove contacts from workflows (Contacts API) |

---

## Endpoints Summary

| Endpoint | Method | Scope | Description |
|----------|--------|-------|-------------|
| [Get Workflows](#get-workflows) | GET | workflows.readonly | List all workflows in a location |
| [Add Contact to Workflow](#add-contact-to-workflow) | POST | contacts.write | Enroll a contact in a workflow *(cross-ref from Contacts API)* |
| [Delete Contact from Workflow](#delete-contact-from-workflow) | DELETE | contacts.write | Remove a contact from a workflow *(cross-ref from Contacts API)* |

---

## Authentication

All requests require:

```
Authorization: Bearer {access_token}
Version: 2021-07-28
```

---

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds |
| Daily Limit | 200,000 requests / day |

---

## Workflow Object Schema

```json
{
  "id": "78c57bfc-4862-4ac2-bf30-65085746dc23",
  "name": "New Lead Follow-Up",
  "status": "published",
  "version": 2,
  "createdAt": "2023-01-15T10:30:00.000Z",
  "updatedAt": "2023-06-20T14:45:00.000Z",
  "locationId": "ve9EPM428h8vShlRW1KT"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Workflow UUID (e.g., `"78c57bfc-4862-4ac2-bf30-65085746dc23"`) |
| `name` | string | Workflow name |
| `status` | string | `"draft"` or `"published"` |
| `version` | number | Workflow version number |
| `createdAt` | string | ISO 8601 timestamp |
| `updatedAt` | string | ISO 8601 timestamp |
| `locationId` | string | Sub-account/location ID |

---

## Get Workflows

List all workflows in a location. Despite the singular name in the API spec, this returns all workflows.

**Method:** `GET`
**URL:** `/workflows/`
**Scope:** `workflows.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Sub-account/location ID |

### Request Example

```bash
curl -X GET \
  'https://services.leadconnectorhq.com/workflows/?locationId=ve9EPM428h8vShlRW1KT' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "workflows": [
    {
      "id": "78c57bfc-4862-4ac2-bf30-65085746dc23",
      "name": "New Lead Follow-Up",
      "status": "published",
      "version": 2,
      "createdAt": "2023-01-15T10:30:00.000Z",
      "updatedAt": "2023-06-20T14:45:00.000Z",
      "locationId": "ve9EPM428h8vShlRW1KT"
    },
    {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "name": "Appointment Reminder",
      "status": "draft",
      "version": 1,
      "createdAt": "2023-03-10T08:00:00.000Z",
      "updatedAt": "2023-03-10T08:00:00.000Z",
      "locationId": "ve9EPM428h8vShlRW1KT"
    }
  ]
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `workflows` | array | Array of workflow objects |

### Important Notes

- There is **no** endpoint to get a single workflow by ID, create, update, or delete workflows via API. Workflow design is UI-only.
- There is **no** endpoint to get workflow execution/run status or logs.
- Workflow IDs use UUID format, unlike most GHL IDs which are alphanumeric strings.

> **Tested:** Verified against a live location. Returns `workflows` array with id, name, status, version, createdAt, updatedAt, locationId. Also returns `traceId` at the top level (not documented in OpenAPI spec).

---

## Add Contact to Workflow

Enrolls a contact into a workflow, triggering its execution for that contact.

> **Note:** This endpoint is part of the Contacts API (`contacts.write` scope). It is documented here for cross-reference. See [contacts.md](contacts.md) for the primary documentation.

**Method:** `POST`
**URL:** `/contacts/{contactId}/workflow/{workflowId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | Contact ID to enroll |
| `workflowId` | string | Yes | Workflow ID to enroll into |

### Request Example

```bash
curl -X POST \
  'https://services.leadconnectorhq.com/contacts/ctc_xyz789ghi012/workflow/78c57bfc-4862-4ac2-bf30-65085746dc23' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "succeedded": true
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `succeedded` | boolean | Whether the contact was enrolled successfully |

**Warning:** The field name `"succeedded"` with a double-d is a **known typo** in the GHL API. This is the actual field name returned -- do not "fix" it in your code.

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Delete Contact from Workflow

Removes a contact from an active workflow.

> **Note:** This endpoint is part of the Contacts API (`contacts.write` scope). It is documented here for cross-reference. See [contacts.md](contacts.md) for the primary documentation.

**Method:** `DELETE`
**URL:** `/contacts/{contactId}/workflow/{workflowId}`
**Scope:** `contacts.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | Contact ID to remove |
| `workflowId` | string | Yes | Workflow ID to remove from |

### Request Example

```bash
curl -X DELETE \
  'https://services.leadconnectorhq.com/contacts/ctc_xyz789ghi012/workflow/78c57bfc-4862-4ac2-bf30-65085746dc23' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Version: 2021-07-28' \
  -H 'Accept: application/json'
```

### Response Example

```json
{
  "succeedded": true
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `succeedded` | boolean | Whether the contact was removed successfully |

> **Status:** 🔬 Unverified — documented from OpenAPI spec research, not tested with live API.

---

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 400 | Bad Request — invalid parameters |
| 401 | Unauthorized — invalid/missing token or insufficient scopes |
| 422 | Unprocessable Entity — validation failure |

---

## Notes

### Workflows vs Campaigns

GHL has two automation systems. Workflows are the current primary system; Campaigns are the legacy system that remains functional.

| Aspect | Campaigns (Legacy) | Workflows (Current) |
|--------|--------------------|---------------------|
| **Logic** | Linear steps with time delays | Visual builder with branching and conditionals |
| **Channels** | Email and SMS only | Multi-channel (email, SMS, calls, webhooks, Slack, custom code, etc.) |
| **Triggers** | Manual/API addition only | Dozens of triggers (forms, tags, appointments, webhooks, etc.) |
| **Complexity** | Low — sequential only | High — full automation engine |
| **Status** | Legacy but functional | Active primary system |
| **API scope** | `campaigns.readonly` | `workflows.readonly` |

### Triggering Workflows Programmatically

There are three methods to trigger workflows via external systems:

1. **Add Contact to Workflow endpoint** (`POST /contacts/{contactId}/workflow/{workflowId}`) — directly enrolls a contact, effectively triggering the workflow for them.
2. **Inbound Webhook trigger** — Workflows can be configured with an "Inbound Webhook" trigger that generates a unique URL. External systems POST data to this URL to fire the workflow.
3. **Marketplace Custom Triggers** — Apps can create custom workflow triggers through the marketplace framework.

### General Notes

- The Workflows API is read-only. There is no way to create, update, or delete workflows via the API. All workflow design happens in the GHL UI.
- Workflow IDs use UUID format (e.g., `78c57bfc-4862-4ac2-bf30-65085746dc23`), which differs from most other GHL entity IDs that use alphanumeric strings.
- The `succeedded` typo in responses from the contact-level endpoints is a known GHL API issue and should be expected in your response parsing.

---

## Outbound Webhook Action — Payload Shape

> **Tested:** Verified against a live webhook fired by an Invoice-triggered workflow.

When a Workflow uses the **Webhook** action (not to be confused with the Inbound Webhook *trigger*), GHL POSTs a JSON body to your URL with **two distinct sections**:

1. **A flat Contact summary** at the top level of the payload — automatic, you cannot opt out.
2. **A `customData` object** containing the key/value pairs you defined in the action's Custom Data section, with merge-field interpolation applied.

There is **no automatic event/trigger payload** alongside the contact data. Despite a `triggerData` field appearing in the body, it arrives as an empty object `{}` for at least Invoice-triggered workflows. **If your endpoint needs trigger-specific data (invoice fields, opportunity fields, appointment fields, etc.), you must pull them through `customData` using merge fields.**

### Example Payload (Invoice trigger, "Sent" status filter)

```json
{
  "contact_id": "EXAMPLEContact000001",
  "first_name": "",
  "last_name": "",
  "full_name": "",
  "email": "user@example.com",
  "phone": "",
  "tags": "",
  "country": "US",
  "date_created": "2026-02-22T23:18:36.266Z",
  "full_address": "",
  "contact_type": "lead",
  "location": {
    "name": "Example Business",
    "address": "123 Example St",
    "city": "Exampleville",
    "state": "CA",
    "country": "US",
    "postalCode": "00000",
    "fullAddress": "123 Example St, Exampleville CA 00000",
    "id": "EXAMPLELocation00000"
  },
  "workflow": {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "Example Invoice Workflow"
  },
  "triggerData": {},
  "customData": {
    "type": "sent",
    "invoiceId": "123123",
    "amount": "123.00",
    "total_amount": "123.00",
    "title": "Testing invoice"
  }
}
```

### Field-by-field

#### Top-level Contact fields (snake_case, automatic)

| Field | Type | Notes |
|-------|------|-------|
| `contact_id` | string | Contact's GHL ID. **snake_case** — not `contactId` |
| `first_name` | string | May be empty string if not set |
| `last_name` | string | May be empty string if not set |
| `full_name` | string | May be empty string if neither name field is set |
| `email` | string | |
| `phone` | string | |
| `tags` | string | Comma-separated, or empty string if no tags |
| `country` | string | ISO 2-letter code |
| `date_created` | string | ISO 8601 timestamp |
| `full_address` | string | Composed address, may be empty |
| `contact_type` | string | `"lead"`, `"customer"`, etc. |

> **In practice the payload also includes every contact custom field as a flat key**, e.g. `"SC - Child 1 Age": ""`, `"Wedding Date:": ""`, `"Arcade Waiver Signed Date": "2026-01-13"`. This can balloon the payload to hundreds of keys for sub-accounts with rich custom-field setups. Your endpoint should ignore any fields it does not recognize.

#### Envelope objects (top-level)

| Field | Type | Description |
|-------|------|-------------|
| `location` | object | Sub-account info: `{ id, name, address, city, state, country, postalCode, fullAddress }` |
| `workflow` | object | The firing workflow: `{ id, name }` |
| `triggerData` | object | **Empty `{}` for at least Invoice triggers — do not depend on this field** |
| `customData` | object | Your custom merge-field key/value pairs (see below) |

#### `customData` — the only place trigger-specific data lives

Whatever key/value pairs you define in the workflow's Custom Data section land here, with merge-field placeholders interpolated. **Values are always strings** (numbers like `"123.00"` arrive quoted), so receiving endpoints should `parseFloat()` numeric fields.

### Merge Fields That Work in Custom Data

Verified merge fields for Invoice-triggered workflows:

| Merge Field | Resolves To | Example |
|-------------|-------------|---------|
| `{{invoice.status}}` | Raw status string, lowercase | `"sent"`, `"paid"`, `"partially paid"`, `"viewed"`, `"void"` |
| `{{invoice._id}}` | Invoice's GHL ID | (use the picker — exact merge syntax can vary) |
| `{{invoice.amount}}` | Invoice total amount | `"123.00"` (quoted string) |
| `{{invoice.amount_paid}}` | Cumulative amount paid | `"0.00"` on Sent, full amount on Paid, partial amount on Partially Paid |
| `{{invoice.name}}` | Invoice title/description | `"Testing invoice"` |

> **Always use the merge-field picker (the tag icon next to each value field)** instead of typing merge syntax by hand. Different sub-accounts and CRM versions expose slightly different merge-field paths, and typos fail silently — the placeholder ships unresolved (e.g., the literal string `{{invoice._id}}`) instead of the value.

### Practical Implications for Receiving Endpoints

If you're building an endpoint that receives a Workflow webhook:

1. **Read `payload.customData.*` first**, not the top level, for trigger-specific fields. The customData wrapper is required.
2. **Use snake_case for contact fields**: `payload.contact_id`, `payload.full_name`, `payload.email`. Do not look for `contactId` or `contactName`.
3. **Coerce all customData values to strings** before parsing. `parseFloat(payload.customData.amount)` is the safe pattern for numerics.
4. **Do not rely on `triggerData`.** Treat it as always-empty for now.
5. **Tolerate field bloat.** A sub-account with hundreds of contact custom fields will send all of them at the top level. Ignore unknown keys.
6. **Workflow trigger filters do not get auto-passed in the payload.** If your workflow filters on `Invoice Status = Sent`, you still have to add `type: {{invoice.status}}` to customData if you want your endpoint to know which status fired it. The trigger filter is purely a CRM-side gate — it does not enrich the payload.
7. **Idempotency:** `workflow.id` + the entity ID from customData is the safest dedup key. The Webhook action does not include a unique event ID.

### Worked Example — Wiring an Invoice Workflow to an Endpoint

**CRM-side workflow setup:**

- Trigger: `Invoice` with filter `Invoice Status = Sent`, `Paid`, `Partially Paid` (all three values in one filter, OR-logic)
- Action: Webhook
  - Method: POST
  - URL: `https://your-endpoint.example.com/api/webhooks/invoice`
  - Custom Data:
    ```
    type:         {{invoice.status}}
    invoiceId:    {{invoice._id}}
    amount:       {{invoice.amount}}
    total_amount: {{invoice.amount_paid}}
    title:        {{invoice.name}}
    ```
  - Headers: (typically empty unless you implement HMAC verification on your endpoint)

**Endpoint extraction pattern (Node/Express):**

```js
app.post('/api/webhooks/invoice', express.json(), async (req, res) => {
  res.status(200).json({ success: true });

  const payload = req.body || {};
  const customData = payload.customData || {};

  const invoiceId = customData.invoiceId;
  const contactId = payload.contact_id;
  const contactName = payload.full_name || null;
  const invoiceAmount = parseFloat(customData.amount) || null;
  const totalPaid = parseFloat(customData.total_amount) || 0;
  const status = String(customData.type || '').toLowerCase().trim();

  const isCreated = status === 'sent';
  const isPaid = status === 'paid' || status === 'partially paid';

  // ... persist or process accordingly
});
```

### Caveats

- This shape was verified against an **Invoice-triggered** workflow only. Other triggers (Form Submitted, Opportunity Stage Changed, Appointment Status Changed, etc.) likely follow the same envelope but populate `triggerData` differently. Test each trigger type before depending on the structure.
- The Workflow Webhook action is an **outbound HTTP POST from GHL**, not the Inbound Webhook trigger (which is the reverse direction). Don't confuse them.
- The User-Agent header on outbound Workflow webhooks is `axios/1.13.2` (subject to change).
- The originating IPs observed are in the Google Cloud Platform range (`34.60.x.x`).
- The CRM does **not** sign Workflow webhook payloads by default. If you need authenticity guarantees, add a custom header with a shared secret and verify it on your endpoint.

### Receiver-side gotchas (Express / Node / Railway)

If you're building a Node.js + Express receiver hosted on Railway (or any container platform), there are a few traps that will cause silent failures or hard-to-diagnose 4xx errors. **Most of these are not GHL bugs — they're how every webhook receiver has to be hardened, but the bloat behavior of GHL Workflow webhooks makes them bite faster than they would otherwise.**

#### Body size limit — the #1 silent killer

GHL Workflow webhook payloads can be **massive** when the contact has a lot of custom fields. Every contact custom field is included flat at the top level of the payload alongside the standard contact summary. A sub-account with a few hundred custom fields easily produces a 30-100 KB+ payload, even if you only added a handful of `customData` keys.

**Express's default body limit is 100 KB**, and many production codebases tighten that further. If your app sets a small global limit (e.g., `app.use(express.json({ limit: '10kb' }))`), the webhook will return **413 PayloadTooLargeError** before your handler ever runs. The error response is HTML (Express's default 4xx page), not JSON.

**The trap with route-level overrides:**

A global `app.use(express.json({ limit: '10kb' }))` runs BEFORE any route-specific middleware. Adding a route-level override like:

```js
app.post('/api/webhooks/invoice', express.json({ limit: '5mb' }), handler);
```

is a **no-op** in this configuration — the global parser has already consumed (and rejected) the body. The fix has to be applied to the global registration, OR the webhook route has to be registered before the global parser, OR the global parser has to be removed entirely and applied per-route.

**Recommended approach:**

```js
// In your main app setup, set the global limit high enough for webhooks:
app.use(express.json({ limit: '5mb' }));

// 5MB matches a reasonable middle ground:
//   Stripe receivers: 1MB default
//   GitHub:           25MB
//   GHL Workflow:     observed ~30-100KB typical, can spike higher with rich custom fields
//   5MB:              safe for any sub-account, still bounded against abuse
```

If you need to keep a tight global limit for security/sanity reasons, override per-route using a different parser instance:

```js
// Tight global default
app.use(express.json({ limit: '10kb' }));

// But use a separate parser for webhook routes — declare the parser BEFORE the global, OR mount the route on a sub-app
const webhookJson = express.json({ limit: '5mb' });
app.post('/api/webhooks/invoice', webhookJson, handler);
// ⚠️  This still won't work if the global app.use(express.json) runs first.
//     Either skip the global on webhook paths or mount webhooks on a Router registered before the global.
```

The safest pattern for a project that has both small admin endpoints and fat webhook endpoints:

```js
// Apply json parsing per-route, never globally
app.post('/api/webhooks/invoice', express.json({ limit: '5mb' }), invoiceHandler);
app.post('/api/admin/foo',        express.json({ limit: '10kb' }), adminFooHandler);
// No app.use(express.json(...)) anywhere
```

**Diagnostic for the 413 trap:**

The error response is a giant HTML stack trace. Look for these two lines together:

```
PayloadTooLargeError: request entity too large
  at jsonParser (.../body-parser/lib/types/json.js:138:5)
```

If you see `jsonParser` in the stack but the URL is your webhook route, the global parser is rejecting before the route runs. Check your `app.use(express.json(...))` lines.

#### Respond-first, process-async

The GHL workflow expects a 2xx response within seconds. If your handler does DB writes, third-party API calls, or other slow work synchronously, the workflow Execution Log will show errors even though everything technically worked.

**Pattern:**

```js
app.post('/api/webhooks/invoice', express.json({ limit: '5mb' }), async (req, res) => {
  // 1. Acknowledge IMMEDIATELY so the workflow sees success
  res.status(200).json({ success: true, message: 'Webhook received' });

  // 2. Then process async — wrap in try/catch, do NOT throw out
  try {
    await processInvoiceWebhook(req.body);
  } catch (err) {
    console.error('[Webhook:Invoice] Processing failed:', err);
    // Log it, alert it, but don't try to respond — already responded
  }
});
```

#### Idempotency

GHL does not include a unique event ID on outbound Workflow webhooks. If a workflow re-runs (manual re-fire, retry, contact re-enrolled), your endpoint will receive the same payload again.

**Use the entity ID + workflow ID as a deduplication key:**

```js
// Postgres example
await pool.query(
  `INSERT INTO party_bookings (invoice_id, ...)
   VALUES ($1, ...)
   ON CONFLICT (invoice_id) DO NOTHING`,
  [invoiceId, ...]
);
```

For mutations (e.g., advancing payment status), pattern your update SQL so re-running it is safe:

```js
UPDATE party_bookings
   SET payment_status = $1, paid_at = NOW()
 WHERE invoice_id = $2
   AND payment_status != 'paid'  -- don't re-touch already-paid rows
```

#### Custom Data field-name precedence

When you map GHL merge fields into `customData`, the values arrive as **strings** (even numerics like `"123.00"`). Your handler needs to:

1. Read from `payload.customData.*` first — that's where your trigger-specific data lives
2. Fall back to `payload.contact_id` / `payload.full_name` / etc. for contact info (snake_case at top level)
3. `parseFloat()` numerics, `String(...)` string-coerce booleans, etc.

A robust extraction function:

```js
function extractInvoicePayload(payload) {
  const customData = payload.customData || {};

  return {
    invoiceId:     customData.invoiceId || payload.invoiceId,
    contactId:     customData.contactId || payload.contact_id || payload.contactId,
    contactName:   customData.contactName || payload.full_name || payload.contactName || null,
    invoiceAmount: parseFloat(customData.amount || payload.amount) || null,
    amountPaid:    parseFloat(customData.total_amount || payload.total_amount) || 0,
    invoiceTitle:  customData.title || payload.title || null,
    rawType:       String(customData.type || payload.type || '').toLowerCase().trim(),
  };
}
```

#### Logging the raw payload (essential for debugging)

Always log the full inbound payload to a bounded in-memory buffer so you can inspect what GHL actually sent. The Workflow's own Execution Log shows the workflow's view of the request — your buffer shows what your endpoint actually received, including any platform-side mutation.

```js
const WEBHOOK_LOG = { invoice: [], maxEntries: 100 };

app.post('/api/webhooks/invoice', express.json({ limit: '5mb' }), async (req, res) => {
  const logEntry = {
    timestamp: new Date().toISOString(),
    payload: req.body,
    headers: req.headers,
    rejected: false,
  };
  WEBHOOK_LOG.invoice.unshift(logEntry);
  if (WEBHOOK_LOG.invoice.length > WEBHOOK_LOG.maxEntries) {
    WEBHOOK_LOG.invoice = WEBHOOK_LOG.invoice.slice(0, WEBHOOK_LOG.maxEntries);
  }
  // ... rest of handler
});

// Expose a debug endpoint to inspect recent webhooks
app.get('/api/webhooks/invoice/log', (req, res) => {
  res.json({ count: WEBHOOK_LOG.invoice.length, webhooks: WEBHOOK_LOG.invoice });
});
```

#### Railway-specific notes

- **Railway proxies all traffic through their edge before reaching your container.** The platform itself does not impose a small body-size limit on POST traffic — large webhooks reach your app intact. The 413 is your Express config, not Railway.
- **Auto-deploys on git push to your main branch** are typical. After pushing a fix, allow ~60-90 seconds before testing — Railway's build + boot is sequential.
- **Logs are tail-accessible via `railway logs`** (CLI) or the dashboard. The webhook payload in your in-memory buffer (above) survives across requests within a deploy but resets on each redeploy. For permanent audit trails, persist webhook receipts to your database.
- **Use the Railway custom domain** (e.g., `booking.yourdomain.com`) as the webhook URL in GHL, not the internal `*.railway.app` domain. The internal one works but breaks if Railway re-provisions the service. Custom domain is stable.

### Checklist for a New GHL → Express + Railway Webhook Receiver

Use this when wiring up a new Workflow webhook integration:

- [ ] Express body-parser limit is **at least 5mb** for the webhook route (global or route-level — see traps above)
- [ ] Handler responds with `200 OK` BEFORE any DB or third-party work
- [ ] All async processing wrapped in try/catch — never throw after responding
- [ ] Extraction function reads `customData.*` first with snake_case fallback
- [ ] All numeric `customData` values run through `parseFloat()`
- [ ] Idempotency key set up (`ON CONFLICT DO NOTHING` or equivalent)
- [ ] In-memory webhook log buffer + debug endpoint for inspection
- [ ] Webhook URL uses the platform's stable public domain (custom domain on Railway, not `*.railway.app`)
- [ ] Tested with a synthetic large payload (60KB+) before going live, not just a small JSON sample
- [ ] Workflow's Custom Data merge fields picked from the GHL picker (NOT typed by hand) to avoid silent merge-field typos that ship as literal `{{...}}` strings

---

### General Notes

- The Workflows API is read-only. There is no way to create, update, or delete workflows via the API. All workflow design happens in the GHL UI.
- Workflow IDs use UUID format (e.g., `78c57bfc-4862-4ac2-bf30-65085746dc23`), which differs from most other GHL entity IDs that use alphanumeric strings.
- The `succeedded` typo in responses from the contact-level endpoints is a known GHL API issue and should be expected in your response parsing.

---

> **Agent Note:** Documentation created from OpenAPI spec research plus live verification of the Workflow outbound webhook payload. All API endpoints listed above are 🔬 unverified until tested against a live GHL API. The Outbound Webhook Action section is ✅ verified against production traffic. If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:** {date}` note to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.
