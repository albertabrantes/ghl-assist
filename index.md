# GHL API Endpoint Index

This is the master tracking document for all GoHighLevel API endpoint documentation.

## Status Legend
- ❌ Not Started
- 🔄 In Progress
- ✅ Documented
- 🔬 Documented but Unverified (from research/official docs, not tested with live API calls — agents: please test, confirm, and update)

---

## Which file for what

Quick routing table — open the listed file/folder for a given GHL area.

| API / topic | Doc path |
|-------------|----------|
| Calendars (availability, events/appointments, groups, resources, notifications) | layer-2-enriched-docs/calendars/README.md |
| Campaigns | layer-2-enriched-docs/campaigns.md |
| Contacts (CRUD, tags, notes, tasks, search, campaigns/workflows, webhooks) | layer-2-enriched-docs/contacts/README.md |
| Conversations (messages, search, Conversation AI) | layer-2-enriched-docs/conversations.md |
| Courses | layer-2-enriched-docs/courses.md |
| Custom Fields (legacy + V2, field types, folders) | layer-2-enriched-docs/custom-fields/README.md |
| Custom Values | layer-2-enriched-docs/custom-values.md |
| File Uploads (cross-API guide) | layer-2-enriched-docs/file-uploads.md |
| Forms | layer-2-enriched-docs/forms.md |
| Funnels | layer-2-enriched-docs/funnels.md |
| Invoices (core, templates, schedules, text2pay, estimates) | layer-2-enriched-docs/invoices/README.md |
| Locations (core, custom fields/values, tags, templates, tasks) | layer-2-enriched-docs/locations/README.md |
| Medias | layer-2-enriched-docs/medias.md |
| Custom Objects | layer-2-enriched-docs/objects.md |
| OAuth / Authentication | layer-2-enriched-docs/oauth.md |
| Opportunities (and Pipelines) | layer-2-enriched-docs/opportunities.md |
| Payments (orders, transactions, subscriptions, integrations) | layer-2-enriched-docs/payments/README.md |
| Products (core, prices/inventory, collections, reviews, store) | layer-2-enriched-docs/products/README.md |
| Social Media Posting | layer-2-enriched-docs/social-media-posting.md |
| Store (shipping carriers/zones/rates, store settings) | layer-2-enriched-docs/store/README.md |
| Surveys | layer-2-enriched-docs/surveys.md |
| Users | layer-2-enriched-docs/users.md |
| Voice AI | layer-2-enriched-docs/voice-ai.md |
| Workflows | layer-2-enriched-docs/workflows.md |

---

## Custom Objects API — 9 Endpoints
📄 **Documentation:** [objects.md](layer-2-enriched-docs/objects.md)

> **Tested:** GET /objects/, GET /objects/{key}, GET /objects/{schemaKey}/records/{id}, and POST /objects/{schemaKey}/records/search all verified against a live sub-account.

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 1 | Get All Objects | GET | `/objects/` | ✅ |
| 2 | Create Custom Object Type | POST | `/objects/` | 🔬 (agency-only) |
| 3 | Get Object Schema | GET | `/objects/{key}` | ✅ |
| 4 | Update Object Schema | PUT | `/objects/{key}` | 🔬 |
| 5 | Get Record by ID | GET | `/objects/{schemaKey}/records/{id}` | ✅ |
| 6 | Create Record | POST | `/objects/{schemaKey}/records` | 🔬 |
| 7 | Update Record | PUT | `/objects/{schemaKey}/records/{id}` | 🔬 |
| 8 | Delete Record | DELETE | `/objects/{schemaKey}/records/{id}` | 🔬 |
| 9 | Search Records | POST | `/objects/{schemaKey}/records/search` | ✅ |

---

## OAuth API — 3 Endpoints
📄 **Documentation:** [oauth.md](layer-2-enriched-docs/oauth.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 1 | Get Access Token | POST | `/oauth/token` | 🔬 |
| 2 | Get Location Access Token | POST | `/oauth/locationToken` | 🔬 |
| 3 | Get Installed Locations | GET | `/oauth/installedLocations` | 🔬 |

## Contacts API
📄 **Documentation:** [contacts](layer-2-enriched-docs/contacts/README.md)

| Endpoint | Method | Status |
|----------|--------|--------|
| Create Contact | POST | ✅ |
| Get Contact | GET | ✅ |
| Update Contact | PUT | ✅ |
| Delete Contact | DELETE | ✅ |
| Upsert Contact | POST | ✅ |
| Search Contacts | POST | ✅ |
| Get Contacts by Business | GET | ✅ |
| Add Tags | POST | ✅ |
| Remove Tags | DELETE | ✅ |
| Get All Notes | GET | ✅ |
| Get Note | GET | ✅ |
| Create Note | POST | ✅ |
| Update Note | PUT | ✅ |
| Delete Note | DELETE | ✅ |
| Get All Tasks | GET | ✅ |
| Get Task | GET | ✅ |
| Create Task | POST | ✅ |
| Update Task | PUT | ✅ |
| Update Task Status | PUT | ✅ |
| Delete Task | DELETE | ✅ |
| Get Appointments | GET | ✅ |
| Add to Campaign | POST | ✅ |
| Remove from Campaign | DELETE | ✅ |
| Remove from All Campaigns | DELETE | ✅ |
| Add to Workflow | POST | ✅ |
| Remove from Workflow | DELETE | ✅ |
| Bulk Update Tags | POST | ✅ |

## Voice AI API
📄 **Documentation:** [voice-ai.md](layer-2-enriched-docs/voice-ai.md)

| Endpoint | Method | Status |
|----------|--------|--------|
| List Call Logs | GET | ✅ |
| Get Call Log | GET | ✅ |
| Get Agent Config | GET | ✅ |
| Update Agent Prompt | PATCH | ✅ |

## Conversations API

> **Note:** Comprehensive conversations documentation is in [conversations.md](layer-2-enriched-docs/conversations.md). Legacy docs for Search Conversations and Send Message (SMS) remain in [voice-ai.md](layer-2-enriched-docs/voice-ai.md) for backward compatibility.

### Inbound Messages
| Endpoint | Method | Status |
|----------|--------|--------|
| Add Inbound Message (V1) | POST | ✅ 🔬 |
| Add Inbound Message (V2) | POST | ✅ 🔬 |

### Outbound Messages
| Endpoint | Method | Status |
|----------|--------|--------|
| Send Outbound Message | POST | ✅ |
| Send Message (SMS) | POST | ✅ |

### Message Retrieval & Search
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Messages | GET | ✅ 🔬 |
| Search Conversations | GET | ✅ |

### Conversation AI API
| Endpoint | Method | Status |
|----------|--------|--------|
| Search AI Agents | GET | ✅ 🔬 |
| Get AI Agent | GET | ✅ 🔬 |
| Create AI Agent | POST | ✅ 🔬 |
| Update AI Agent | PUT | ✅ 🔬 |
| Delete AI Agent | DELETE | ✅ 🔬 |
| Get AI Generations | GET | ✅ 🔬 |

> **Status key:** ✅ = Documented, 🔬 = Unverified (from docs/research, not tested with live API calls)

## Calendars API
📄 **Documentation:** [calendars](layer-2-enriched-docs/calendars/README.md)

### Calendars (Core)
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Calendars | GET | ✅ |
| Get Calendar | GET | ✅ |
| Create Calendar | POST | ✅ |
| Update Calendar | PUT | ✅ |
| Delete Calendar | DELETE | ✅ |
| Get Free Slots | GET | ✅ |

### Calendar Events / Appointments

| Endpoint | Method | Status |
|----------|--------|--------|
| Create Appointment | POST | ✅ |
| Get Appointment | GET | ✅ |
| Update Appointment | PUT | ✅ |
| Get Calendar Events | GET | ✅ |
| Delete Event | DELETE | ✅ |
| Get Blocked Slots | GET | ✅ |
| Create Block Slot | POST | ✅ |
| Update Block Slot | PUT | ✅ |

### Calendar Groups

| Endpoint | Method | Status |
|----------|--------|--------|
| Create Calendar Group | POST | ✅ |
| Validate Group Slug | POST | ✅ |
| Delete Calendar Group | DELETE | ✅ |

### Calendar Resources (Deprecated - Services V1)

| Endpoint | Method | Status |
|----------|--------|--------|
| List Calendar Resources | GET | ✅ |
| Get Calendar Resource | GET | ✅ |
| Create Calendar Resource | POST | ✅ |
| Update Calendar Resource | PUT | ✅ |
| Delete Calendar Resource | DELETE | ✅ |

### Calendar Notifications

| Endpoint | Method | Status |
|----------|--------|--------|
| Get Notifications | GET | ✅ |
| Update Notification | PUT | ✅ |

### Appointment Notes

| Endpoint | Method | Status |
|----------|--------|--------|
| Get Appointment Notes | GET | ✅ |
| Create Appointment Note | POST | ✅ |
| Update Appointment Note | PUT | ✅ |
| Delete Appointment Note | DELETE | ✅ |

### Availability / Schedules

| Endpoint | Method | Status |
|----------|--------|--------|
| List User Availability Schedules | GET | ✅ |
| Get User Availability Schedule | GET | ✅ |
| Get Event Calendar Availability Schedule | GET | ✅ |
| Create User Availability Schedule | POST | ✅ |
| Update User Availability Schedule | PUT | ✅ |
| Delete User Availability Schedule | DELETE | ✅ |

## Opportunities API
📄 **Documentation:** [opportunities.md](layer-2-enriched-docs/opportunities.md)

| Endpoint | Method | Status |
|----------|--------|--------|
| Get Pipelines | GET | ✅ |
| Search Opportunities | GET | ✅ |
| Search Opportunities (Advanced) | POST | ✅ |
| Get Opportunity | GET | ✅ |
| Create Opportunity | POST | ✅ |
| Update Opportunity | PUT | ✅ |
| Upsert Opportunity | POST | ✅ |
| Update Opportunity Status | PUT | ✅ |
| Delete Opportunity | DELETE | ✅ |
| Add Followers | POST | ✅ |
| Remove Followers | DELETE | ✅ |

## Pipelines API
📄 **Documentation:** [opportunities.md](layer-2-enriched-docs/opportunities.md)

> **Note:** Pipeline management is part of the Opportunities API. There is no separate Pipelines API for creating/updating pipelines via API.

| Endpoint | Method | Status |
|----------|--------|--------|
| Get Pipelines | GET | ✅ |

## Users API
📄 **Documentation:** [users.md](layer-2-enriched-docs/users.md)

| Endpoint | Method | Status |
|----------|--------|--------|
| Get Users by Location | GET | ✅ |
| Search Users | GET | 🔬 |
| Filter Users by Email | POST | 🔬 |
| Get User | GET | 🔬 |
| Create User | POST | 🔬 |
| Update User | PUT | 🔬 |
| Delete User | DELETE | 🔬 |

## Locations API
📄 **Documentation:** [locations](layer-2-enriched-docs/locations/README.md)

### Search
| Endpoint | Method | Status |
|----------|--------|--------|
| Search Locations | GET | 🔬 |

### Location Core
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Location | GET | 🔬 |
| Create Location | POST | 🔬 |
| Update Location | PUT | 🔬 |
| Delete Location | DELETE | 🔬 |

### Custom Fields
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Custom Fields | GET | 🔬 |
| Get Custom Field | GET | 🔬 |
| Create Custom Field | POST | 🔬 |
| Update Custom Field | PUT | 🔬 |
| Delete Custom Field | DELETE | 🔬 |
| Upload File to Custom Fields | POST | 🔬 |

### Custom Values
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Custom Values | GET | 🔬 |
| Get Custom Value | GET | 🔬 |
| Create Custom Value | POST | 🔬 |
| Update Custom Value | PUT | 🔬 |
| Delete Custom Value | DELETE | 🔬 |

### Tags
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Tags | GET | 🔬 |
| Get Tag by ID | GET | 🔬 |
| Create Tag | POST | 🔬 |
| Update Tag | PUT | 🔬 |
| Delete Tag | DELETE | 🔬 |

### Templates
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Templates | GET | 🔬 |
| Delete Template | DELETE | 🔬 |

### Tasks
| Endpoint | Method | Status |
|----------|--------|--------|
| Task Search | POST | 🔬 |

### Recurring Tasks
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Recurring Task | GET | 🔬 |
| Create Recurring Task | POST | 🔬 |
| Update Recurring Task | PUT | 🔬 |
| Delete Recurring Task | DELETE | 🔬 |

### Timezone
| Endpoint | Method | Status |
|----------|--------|--------|
| Fetch Timezones | GET | 🔬 |

## File Uploads (Cross-API Guide)
📄 **Documentation:** [file-uploads.md](layer-2-enriched-docs/file-uploads.md)

Comprehensive guide covering all file upload methods: contact custom fields, opportunity custom fields (two-step workaround), conversation attachments, and media library. Includes tested limitations and things that do NOT work. **Tested.**

## Forms API — 3 Endpoints
📄 **Documentation:** [forms.md](layer-2-enriched-docs/forms.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 1 | Get Forms | GET | `/forms/` | 🔬 |
| 2 | Get Forms Submissions | GET | `/forms/submissions` | 🔬 |
| 3 | Upload Files to Custom Fields | POST | `/forms/upload-custom-files` | ✅ |

## Surveys API — 2 Endpoints
📄 **Documentation:** [surveys.md](layer-2-enriched-docs/surveys.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 1 | Get Surveys | GET | `/surveys/` | 🔬 |
| 2 | Get Surveys Submissions | GET | `/surveys/submissions` | 🔬 |

## Workflows API — 1 Endpoint
📄 **Documentation:** [workflows.md](layer-2-enriched-docs/workflows.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 1 | Get Workflows | GET | `/workflows/` | 🔬 |

> **See also:** [workflows.md → Outbound Webhook Action — Payload Shape](layer-2-enriched-docs/workflows.md#outbound-webhook-action--payload-shape) (✅ verified) — payload shape, contact summary, customData wrapper, and merge-field reference for the Workflow Webhook *action* (when GHL sends an HTTP POST to your endpoint as part of a workflow execution). Distinct from the Webhooks API and from the Inbound Webhook *trigger*.

## Campaigns API — 1 Endpoint
📄 **Documentation:** [campaigns.md](layer-2-enriched-docs/campaigns.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 1 | Get Campaigns | GET | `/campaigns/` | 🔬 |

## Products API — 27 Endpoints
📄 **Documentation:** [products](layer-2-enriched-docs/products/README.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| | **Products (Core)** | | | |
| 1 | Create Product | POST | `/products/` | 🔬 |
| 2 | List Products | GET | `/products/` | 🔬 |
| 3 | Get Product by ID | GET | `/products/{productId}` | 🔬 |
| 4 | Update Product by ID | PUT | `/products/{productId}` | 🔬 |
| 5 | Delete Product by ID | DELETE | `/products/{productId}` | 🔬 |
| 6 | Bulk Update Products | POST | `/products/bulk-update` | 🔬 |
| 7 | Bulk Edit Products and Prices | POST | `/products/bulk-update/edit` | 🔬 |
| | **Prices** | | | |
| 8 | Create Price | POST | `/products/{productId}/price` | 🔬 |
| 9 | List Prices | GET | `/products/{productId}/price` | 🔬 |
| 10 | Get Price by ID | GET | `/products/{productId}/price/{priceId}` | 🔬 |
| 11 | Update Price by ID | PUT | `/products/{productId}/price/{priceId}` | 🔬 |
| 12 | Delete Price by ID | DELETE | `/products/{productId}/price/{priceId}` | 🔬 |
| 13 | List Inventory | GET | `/products/inventory` | 🔬 |
| 14 | Update Inventory | POST | `/products/inventory` | 🔬 |
| | **Collections** | | | |
| 15 | List Collections | GET | `/products/collections` | 🔬 |
| 16 | Create Collection | POST | `/products/collections` | 🔬 |
| 17 | Get Collection | GET | `/products/collections/{collectionId}` | 🔬 |
| 18 | Update Collection | PUT | `/products/collections/{collectionId}` | 🔬 |
| 19 | Delete Collection | DELETE | `/products/collections/{collectionId}` | 🔬 |
| | **Reviews** | | | |
| 20 | List Reviews | GET | `/products/reviews` | 🔬 |
| 21 | Bulk Update Reviews | POST | `/products/reviews/bulk-update` | 🔬 |
| 22 | Get Review Count | GET | `/products/reviews/count` | 🔬 |
| 23 | Update Review | PUT | `/products/reviews/{reviewId}` | 🔬 |
| 24 | Delete Review | DELETE | `/products/reviews/{reviewId}` | 🔬 |
| | **Store** | | | |
| 25 | Include/Exclude Product in Store | POST | `/products/store/{storeId}` | 🔬 |
| 26 | Update Store Display Priorities | POST | `/products/store/{storeId}/priority` | 🔬 |
| 27 | Get Store Stats | GET | `/products/store/{storeId}/stats` | 🔬 |

## Store API — 18 Endpoints
📄 **Documentation:** [store](layer-2-enriched-docs/store/README.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| | **Shipping Carriers** | | | |
| 1 | Create Shipping Carrier | POST | `/store/shipping-carrier` | 🔬 |
| 2 | List Shipping Carriers | GET | `/store/shipping-carrier` | 🔬 |
| 3 | Get Shipping Carrier | GET | `/store/shipping-carrier/{shippingCarrierId}` | 🔬 |
| 4 | Update Shipping Carrier | PUT | `/store/shipping-carrier/{shippingCarrierId}` | 🔬 |
| 5 | Delete Shipping Carrier | DELETE | `/store/shipping-carrier/{shippingCarrierId}` | 🔬 |
| | **Shipping Zones** | | | |
| 6 | Create Shipping Zone | POST | `/store/shipping-zone` | 🔬 |
| 7 | List Shipping Zones | GET | `/store/shipping-zone` | 🔬 |
| 8 | Get Shipping Zone | GET | `/store/shipping-zone/{shippingZoneId}` | 🔬 |
| 9 | Update Shipping Zone | PUT | `/store/shipping-zone/{shippingZoneId}` | 🔬 |
| 10 | Delete Shipping Zone | DELETE | `/store/shipping-zone/{shippingZoneId}` | 🔬 |
| 11 | Get Available Shipping Rates | POST | `/store/shipping-zone/shipping-rates` | 🔬 |
| | **Shipping Zone Rates** | | | |
| 12 | Create Shipping Rate | POST | `/store/shipping-zone/{shippingZoneId}/shipping-rate` | 🔬 |
| 13 | List Shipping Rates | GET | `/store/shipping-zone/{shippingZoneId}/shipping-rate` | 🔬 |
| 14 | Get Shipping Rate | GET | `/store/shipping-zone/{shippingZoneId}/shipping-rate/{shippingRateId}` | 🔬 |
| 15 | Update Shipping Rate | PUT | `/store/shipping-zone/{shippingZoneId}/shipping-rate/{shippingRateId}` | 🔬 |
| 16 | Delete Shipping Rate | DELETE | `/store/shipping-zone/{shippingZoneId}/shipping-rate/{shippingRateId}` | 🔬 |
| | **Store Settings** | | | |
| 17 | Create/Update Store Settings | POST | `/store/store-setting` | 🔬 |
| 18 | Get Store Settings | GET | `/store/store-setting` | 🔬 |

## Funnels API — 7 Endpoints
📄 **Documentation:** [funnels.md](layer-2-enriched-docs/funnels.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| | **Funnels** | | | |
| 1 | Fetch List of Funnels | GET | `/funnels/funnel/list` | 🔬 |
| 2 | Fetch List of Funnel Pages | GET | `/funnels/page` | 🔬 |
| 3 | Fetch Count of Funnel Pages | GET | `/funnels/page/count` | 🔬 |
| | **Redirects** | | | |
| 4 | Create Redirect | POST | `/funnels/lookup/redirect` | 🔬 |
| 5 | Update Redirect By Id | PATCH | `/funnels/lookup/redirect/{id}` | 🔬 |
| 6 | Delete Redirect By Id | DELETE | `/funnels/lookup/redirect/{id}` | 🔬 |
| 7 | Fetch List of Redirects | GET | `/funnels/lookup/redirect/list` | 🔬 |

## Invoices API — 42 Endpoints
📄 **Documentation:** [invoices](layer-2-enriched-docs/invoices/README.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| | **Invoice Core** | | | |
| 1 | Generate Invoice Number | GET | `/invoices/generate-invoice-number` | 🔬 |
| 2 | Create Invoice | POST | `/invoices/` | 🔬 |
| 3 | List Invoices | GET | `/invoices/` | 🔬 |
| 4 | Get Invoice | GET | `/invoices/{invoiceId}` | 🔬 |
| 5 | Update Invoice | PUT | `/invoices/{invoiceId}` | ✅ |
| 6 | Delete Invoice | DELETE | `/invoices/{invoiceId}` | 🔬 |
| 7 | Void Invoice | POST | `/invoices/{invoiceId}/void` | 🔬 |
| 8 | Send Invoice | POST | `/invoices/{invoiceId}/send` | 🔬 |
| 9 | Record Manual Payment | POST | `/invoices/{invoiceId}/record-payment` | 🔬 |
| 10 | Update Invoice Late Fees Config | PATCH | `/invoices/{invoiceId}/late-fees-configuration` | 🔬 |
| 11 | Update Invoice Last Visited At | PATCH | `/invoices/stats/last-visited-at` | 🔬 |
| | **Invoice Templates** | | | |
| 12 | Create Template | POST | `/invoices/template` | 🔬 |
| 13 | List Templates | GET | `/invoices/template` | 🔬 |
| 14 | Get Template | GET | `/invoices/template/{templateId}` | 🔬 |
| 15 | Update Template | PUT | `/invoices/template/{templateId}` | 🔬 |
| 16 | Delete Template | DELETE | `/invoices/template/{templateId}` | 🔬 |
| 17 | Update Template Late Fees Config | PATCH | `/invoices/template/{templateId}/late-fees-configuration` | 🔬 |
| 18 | Update Template Payment Methods Config | PATCH | `/invoices/template/{templateId}/payment-methods-configuration` | 🔬 |
| | **Invoice Schedules** | | | |
| 19 | Create Invoice Schedule | POST | `/invoices/schedule` | 🔬 |
| 20 | List Schedules | GET | `/invoices/schedule` | 🔬 |
| 21 | Get Schedule | GET | `/invoices/schedule/{scheduleId}` | 🔬 |
| 22 | Update Schedule | PUT | `/invoices/schedule/{scheduleId}` | 🔬 |
| 23 | Delete Schedule | DELETE | `/invoices/schedule/{scheduleId}` | 🔬 |
| 24 | Update and Reschedule | POST | `/invoices/schedule/{scheduleId}/updateAndSchedule` | 🔬 |
| 25 | Activate Schedule | POST | `/invoices/schedule/{scheduleId}/schedule` | 🔬 |
| 26 | Manage Auto Payment | POST | `/invoices/schedule/{scheduleId}/auto-payment` | 🔬 |
| 27 | Cancel Schedule | POST | `/invoices/schedule/{scheduleId}/cancel` | 🔬 |
| | **Text2Pay** | | | |
| 28 | Create and Send Text2Pay | POST | `/invoices/text2pay` | 🔬 |
| | **Estimates** | | | |
| 29 | Create Estimate | POST | `/invoices/estimate` | 🔬 |
| 30 | Update Estimate | PUT | `/invoices/estimate/{estimateId}` | 🔬 |
| 31 | Delete Estimate | DELETE | `/invoices/estimate/{estimateId}` | 🔬 |
| 32 | Generate Estimate Number | GET | `/invoices/estimate/number/generate` | 🔬 |
| 33 | Send Estimate | POST | `/invoices/estimate/{estimateId}/send` | 🔬 |
| 34 | Create Invoice from Estimate | POST | `/invoices/estimate/{estimateId}/invoice` | 🔬 |
| 35 | List Estimates | GET | `/invoices/estimate/list` | 🔬 |
| 36 | Update Estimate Last Visited At | PATCH | `/invoices/estimate/stats/last-visited-at` | 🔬 |
| 37 | List Estimate Templates | GET | `/invoices/estimate/template` | 🔬 |
| 38 | Create Estimate Template | POST | `/invoices/estimate/template` | 🔬 |
| 39 | Update Estimate Template | PUT | `/invoices/estimate/template/{templateId}` | 🔬 |
| 40 | Delete Estimate Template | DELETE | `/invoices/estimate/template/{templateId}` | 🔬 |
| 41 | Preview Estimate Template | GET | `/invoices/estimate/template/preview` | 🔬 |
| 42 | Get Estimate | GET | `/invoices/estimate/{estimateId}` | 🔬 |

## Payments API — 24 Endpoints
📄 **Documentation:** [payments](layer-2-enriched-docs/payments/README.md)

| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| | **White-label Integrations** | | | |
| 1 | Create White-label Integration Provider | POST | `/payments/integrations/provider/whitelabel` | 🔬 |
| 2 | List White-label Integration Providers | GET | `/payments/integrations/provider/whitelabel` | 🔬 |
| | **Orders** | | | |
| 3 | List Orders | GET | `/payments/orders` | 🔬 |
| 4 | Get Order by ID | GET | `/payments/orders/{orderId}` | 🔬 |
| 5 | Record Order Payment | POST | `/payments/orders/{orderId}/record-payment` | 🔬 |
| 6 | Migrate Order Payment Status | POST | `/payments/orders/migrate-order-ps` | 🔬 |
| | **Order Fulfillments** | | | |
| 7 | Create Order Fulfillment | POST | `/payments/orders/{orderId}/fulfillments` | 🔬 |
| 8 | List Order Fulfillments | GET | `/payments/orders/{orderId}/fulfillments` | 🔬 |
| | **Order Notes** | | | |
| 9 | List Order Notes | GET | `/payments/orders/{orderId}/notes` | 🔬 |
| | **Transactions** | | | |
| 10 | List Transactions | GET | `/payments/transactions` | 🔬 |
| 11 | Get Transaction by ID | GET | `/payments/transactions/{transactionId}` | 🔬 |
| | **Subscriptions** | | | |
| 12 | List Subscriptions | GET | `/payments/subscriptions` | 🔬 |
| 13 | Get Subscription by ID | GET | `/payments/subscriptions/{subscriptionId}` | 🔬 |
| | **Coupons** | | | |
| 14 | List Coupons | GET | `/payments/coupon/list` | 🔬 |
| 15 | Create Coupon | POST | `/payments/coupon` | 🔬 |
| 16 | Fetch Coupon | GET | `/payments/coupon` | 🔬 |
| 17 | Update Coupon | PUT | `/payments/coupon` | 🔬 |
| 18 | Delete Coupon | DELETE | `/payments/coupon` | 🔬 |
| | **Custom Payment Providers** | | | |
| 19 | Create Custom Provider Integration | POST | `/payments/custom-provider/provider` | 🔬 |
| 20 | Delete Custom Provider Integration | DELETE | `/payments/custom-provider/provider` | 🔬 |
| 21 | Fetch Provider Config | GET | `/payments/custom-provider/connect` | 🔬 |
| 22 | Create Provider Config | POST | `/payments/custom-provider/connect` | 🔬 |
| 23 | Disconnect Provider Config | POST | `/payments/custom-provider/disconnect` | 🔬 |
| 24 | Update Marketplace App Capabilities | PUT | `/payments/custom-provider/capabilities` | 🔬 |

## Custom Fields API
📄 **Documentation:** [custom-fields](layer-2-enriched-docs/custom-fields/README.md)

### Legacy API (Contacts/Opportunities)
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Custom Fields | GET | ✅ |
| Get Custom Field | GET | ✅ |
| Create Custom Field | POST | ✅ |
| Update Custom Field | PUT | ✅ |
| Delete Custom Field | DELETE | ✅ |
| Upload File | POST | ✅ |

### V2 API (Custom Objects/Business)
| Endpoint | Method | Status |
|----------|--------|--------|
| Get Fields by Object Key | GET | ✅ |
| Get Custom Field | GET | ✅ |
| Create Custom Field | POST | ✅ |
| Update Custom Field | PUT | ✅ |
| Delete Custom Field | DELETE | ✅ |
| Create Folder | POST | ✅ |
| Update Folder | PUT | ✅ |
| Delete Folder | DELETE | ✅ |

## Custom Values API
📄 **Documentation:** [custom-values.md](layer-2-enriched-docs/custom-values.md)

| Endpoint | Method | Status |
|----------|--------|--------|
| Get All Custom Values | GET | ✅ |
| Get Custom Value | GET | ✅ |
| Create Custom Value | POST | ✅ |
| Update Custom Value | PUT | ✅ |
| Delete Custom Value | DELETE | ✅ |

## Social Media Posting API — 40 Endpoints
📄 **Documentation:** [social-media-posting.md](layer-2-enriched-docs/social-media-posting.md)

### Accounts
| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 1 | Get Accounts | GET | `/social-media-posting/{locationId}/accounts` | ✅ |
| 2 | Delete Account | DELETE | `/social-media-posting/{locationId}/accounts/{id}` | ✅ |

### Posts
| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 3 | List Posts | POST | `/social-media-posting/{locationId}/posts/list` | ✅ |
| 4 | Create Post | POST | `/social-media-posting/{locationId}/posts` | ✅ |
| 5 | Get Post | GET | `/social-media-posting/{locationId}/posts/{id}` | ✅ |
| 6 | Update Post | PUT | `/social-media-posting/{locationId}/posts/{id}` | ✅ |
| 7 | Delete Post | DELETE | `/social-media-posting/{locationId}/posts/{id}` | ✅ |
| 8 | Bulk Delete Posts | POST | `/social-media-posting/{locationId}/posts/bulk-delete` | ✅ |

### Categories
| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 9 | Get Categories | GET | `/social-media-posting/{locationId}/categories` | ✅ |
| 10 | Get Category by ID | GET | `/social-media-posting/{locationId}/categories/{id}` | ✅ |

### Tags
| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 11 | Get Tags | GET | `/social-media-posting/{locationId}/tags` | ✅ |
| 12 | Get Tags by IDs | POST | `/social-media-posting/{locationId}/tags/details` | ✅ |

### Statistics
| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 13 | Get Statistics | POST | `/social-media-posting/statistics` | ✅ |

### OAuth (Google, Facebook, Instagram, LinkedIn, Twitter, TikTok)
| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 14 | Start Google OAuth | GET | `/social-media-posting/oauth/google/start` | 🔬 |
| 15 | Get Google Locations | GET | `/social-media-posting/oauth/{locationId}/google/locations/{accountId}` | 🔬 |
| 16 | Set Google Locations | POST | `/social-media-posting/oauth/{locationId}/google/locations/{accountId}` | 🔬 |
| 17 | Start Facebook OAuth | GET | `/social-media-posting/oauth/facebook/start` | ✅ |
| 18 | Get Facebook Pages | GET | `/social-media-posting/oauth/{locationId}/facebook/accounts/{accountId}` | 🔬 |
| 19 | Attach Facebook Pages | POST | `/social-media-posting/oauth/{locationId}/facebook/accounts/{accountId}` | 🔬 |
| 20 | Start Instagram OAuth | GET | `/social-media-posting/oauth/instagram/start` | 🔬 |
| 21 | Get Instagram Accounts | GET | `/social-media-posting/oauth/{locationId}/instagram/accounts/{accountId}` | 🔬 |
| 22 | Attach Instagram Accounts | POST | `/social-media-posting/oauth/{locationId}/instagram/accounts/{accountId}` | 🔬 |
| 23 | Start LinkedIn OAuth | GET | `/social-media-posting/oauth/linkedin/start` | 🔬 |
| 24 | Get LinkedIn Pages/Profile | GET | `/social-media-posting/oauth/{locationId}/linkedin/accounts/{accountId}` | 🔬 |
| 25 | Attach LinkedIn Pages/Profile | POST | `/social-media-posting/oauth/{locationId}/linkedin/accounts/{accountId}` | 🔬 |
| 26 | Start Twitter OAuth | GET | `/social-media-posting/oauth/twitter/start` | 🔬 |
| 27 | Get Twitter Profile | GET | `/social-media-posting/oauth/{locationId}/twitter/accounts/{accountId}` | 🔬 |
| 28 | Attach Twitter Profile | POST | `/social-media-posting/oauth/{locationId}/twitter/accounts/{accountId}` | 🔬 |
| 29 | Start TikTok OAuth | GET | `/social-media-posting/oauth/tiktok/start` | 🔬 |
| 30 | Get TikTok Profile | GET | `/social-media-posting/oauth/{locationId}/tiktok/accounts/{accountId}` | 🔬 |
| 31 | Attach TikTok Profile | POST | `/social-media-posting/oauth/{locationId}/tiktok/accounts/{accountId}` | 🔬 |
| 32 | Start TikTok Business OAuth | GET | `/social-media-posting/oauth/tiktok-business/start` | 🔬 |
| 33 | Get TikTok Business Profile | GET | `/social-media-posting/oauth/{locationId}/tiktok-business/accounts/{accountId}` | 🔬 |

### CSV Import
| # | Endpoint | Method | Path | Status |
|---|----------|--------|------|--------|
| 34 | Upload CSV | POST | `/social-media-posting/{locationId}/csv` | 🔬 |
| 35 | Get Upload Status | GET | `/social-media-posting/{locationId}/csv` | ✅ |
| 36 | Set Accounts | POST | `/social-media-posting/{locationId}/set-accounts` | 🔬 |
| 37 | Get CSV Post | GET | `/social-media-posting/{locationId}/csv/{id}` | 🔬 |
| 38 | Start CSV Finalize | PATCH | `/social-media-posting/{locationId}/csv/{id}` | 🔬 |
| 39 | Delete CSV | DELETE | `/social-media-posting/{locationId}/csv/{id}` | 🔬 |
| 40 | Delete CSV Post | DELETE | `/social-media-posting/{locationId}/csv/{csvId}/post/{postId}` | 🔬 |

## OAuth / Authentication

> **Note:** The full OAuth API (3 endpoints) is documented at the top of this index. See [OAuth API](#oauth-api--3-endpoints) above.

---

## Progress Summary

| Category | Documented | Total | % Complete | Notes |
|----------|------------|-------|------------|-------|
| Contacts | 27 | 27 | 100% | |
| Voice AI | 4 | 4 | 100% | |
| Conversations (Inbound) | 2 | 2 | 100% | 🔬 Unverified |
| Conversations (Outbound) | 2 | 2 | 100% | SMS verified |
| Conversations (Retrieval) | 2 | 2 | 100% | Search verified, Get Messages 🔬 |
| Conversation AI | 6 | 6 | 100% | 🔬 Unverified |
| Calendars (Core) | 6 | 6 | 100% | |
| Calendar Events | 8 | 8 | 100% | |
| Calendar Groups | 3 | 3 | 100% | |
| Calendar Resources | 5 | 5 | 100% | |
| Calendar Notifications | 2 | 2 | 100% | |
| Appointment Notes | 4 | 4 | 100% | |
| Availability/Schedules | 6 | 6 | 100% | |
| Opportunities | 11 | 11 | 100% | |
| Pipelines | 1 | 1 | 100% | |
| Users | 7 | 7 | 100% | 1 verified, 6 🔬 |
| Locations | 29 | 29 | 100% | 🔬 All unverified |
| Forms | 3 | 3 | 100% | 🔬 All unverified |
| Surveys | 2 | 2 | 100% | 🔬 All unverified |
| Workflows | 1 | 1 | 100% | 🔬 Unverified |
| Campaigns | 1 | 1 | 100% | 🔬 Unverified |
| Funnels | 7 | 7 | 100% | 🔬 All unverified |
| Custom Fields | 14 | 14 | 100% | |
| Custom Values | 5 | 5 | 100% | |
| Products | 27 | 27 | 100% | 🔬 All unverified |
| Store | 18 | 18 | 100% | 🔬 All unverified |
| Invoices | 42 | 42 | 100% | 🔬 All unverified |
| Payments | 24 | 24 | 100% | 🔬 All unverified |
| OAuth | 3 | 3 | 100% | 🔬 All unverified |
| Social Media Posting | 40 | 40 | 100% | 14 verified, 26 🔬 (OAuth/CSV) |
| Custom Objects | 9 | 9 | 100% | 4 verified ✅, 5 🔬 |
| **TOTAL** | **321** | **321** | **100%** | 🔬 = needs live testing |

---

*Note: This index will be expanded as we discover more endpoints from the GHL API documentation.*

---

## Agent Instructions: Continuous Documentation Improvement

**IMPORTANT: Every agent that reads this documentation is expected to help improve it.**

When you use any endpoint documented here and encounter discrepancies, missing information, or errors, you MUST update the documentation before finishing your task. This is how we keep the docs accurate — through real-world usage.

### What to Look For

1. **Wrong field names** — e.g., API expects `contacts` but docs say `contactIds`, or `pageLimit` vs `limit`
2. **Missing required fields** — e.g., `locationId` is required but not listed in the docs
3. **Wrong response schemas** — fields that don't match what the API actually returns
4. **Undocumented endpoints** — if you discover a new endpoint, add it to this index and create documentation
5. **Incorrect status codes** — if an endpoint returns different error codes than documented
6. **Missing scopes** — if an endpoint requires scopes not listed in the docs
7. **Deprecated behavior** — if something has changed since the docs were written

### How to Update

1. **Fix the specific endpoint doc** in `layer-2-enriched-docs/{api-name}.md` (or its folder for split domains)
2. **Add a `> **Tested:**` note** with the date and what you confirmed, e.g.:
   ```
   > **Tested:** 2026-02-19 — Confirmed `contacts` (not `contactIds`) is the correct field name.
   ```
3. **Update this index** if you add new endpoints or change status from 🔬 to ✅
4. **Change status from 🔬 to ✅** once you've verified an endpoint works with a live API call
5. **Add Node.js/Python code examples** when you write working code that calls an endpoint — include them in the endpoint doc under a `### Code Example` section

### Known Gotchas

Hard-won, verified facts. Grouped by API. Each is a present-tense rule — apply before debugging mysterious failures.

**Contacts**
- `POST /contacts/search`: the page-size field is `pageLimit`, NOT `limit` (422 otherwise).
- `POST /contacts/bulk/tags/update/{type}`: the field is `contacts`, NOT `contactIds` (422). `locationId` is required (400 without it).

**Users**
- `GET /users/` requires the `users.readonly` scope on the PIT — not enabled by default.

**Social Media Posting**
- `POST /{loc}/posts`: `accountIds` and `userId` are required even for draft posts (422 without them).
- `PUT /{loc}/posts/{id}`: `userId` is required (422 without it). The response does NOT return the updated post object.
- `GET /{loc}/tags`: `tags` comes back as a string (not an array) when no tags exist.
- `GET /{loc}/csv`: `userId` query param is required (422) despite the OpenAPI spec listing it as optional.

**Forms & File Uploads**
- `POST /forms/upload-custom-files` only accepts `contactId`, NOT `opportunityId`. Passing an opp field ID with a contactId uploads to the contact, not the opportunity.
- `POST /forms/upload-custom-files`: the response misspells "succeeded" as `"succeded"`.
- `PUT /opportunities/{id}`: FILE_UPLOAD custom fields can only be written once. Subsequent updates return 200 but silently ignore the new value.
- `POST /conversations/messages/upload` accepts `contactId` as an alternative to `conversationId` (undocumented). Upload only stages the file; it does NOT create a Documents-tab entry.
- `POST /conversations/messages` with `type: "Custom"` requires `conversationProviderId` (400 without it). There is no clean "internal only" message type.

**Courses**
- `POST /courses/courses-exporter/public/import`: `bucketVideoUrl` only accepts GHL-internal media URLs. External URLs (Gumlet/AWS/Bunny etc.) are silently dropped — the API returns 201 and creates the course/module/post, but the video field is left empty with no error.
- Same endpoint: `visibility` enum is `"published"` / `"draft"`, NOT `"public"` / `"private"` (some marketplace docs show the latter).

**Medias**
- `POST /medias/upload-file`: the "max 25 MB" claim is wrong — it accepts video files up to ~150 MB before a Cloudflare 524 timeout (not a GHL hard limit). The practical ceiling depends on upload speed (must finish < 120s).
- Same endpoint: `hosted: true` + `fileUrl` mode 400s with `UNABLE_TO_DETERMINE_CONTENT_TYPE` when the `fileUrl` rejects HEAD requests (e.g. signed AWS/Gumlet URLs are method-scoped). GHL does a HEAD probe before pulling.

**Workflows — Outbound Webhook action**
- Custom Data fields land under `payload.customData.*`, not the top level.
- Contact fields are flat at the top level in **snake_case** (`contact_id`, `full_name`, `email`) — not nested under a `contact` object, not camelCase.
- `triggerData` arrives as empty `{}` for Invoice triggers — trigger-specific data MUST come through customData merge fields.
- The trigger's filter (e.g. Invoice Status = Sent) does NOT enrich the payload — the endpoint only knows what fired it via customData.
- All customData values are strings, even numerics (`"123.00"`).
- Sub-accounts with many custom fields produce large payloads (every contact custom field is included flat at the top level), easily 30–100 KB+. On an Express receiver, a tight global `express.json({ limit: '10kb' })` returns `413 PayloadTooLargeError`. Adding a route-level `express.json({ limit: '5mb' })` is a **no-op** — the global parser runs first and rejects the body before the route middleware fires. Fix: raise the GLOBAL limit, or remove the global parser and apply per-route. (See workflows.md for the full receiver-side guide.)

**Invoices — `PUT /invoices/{invoiceId}`**
- **Carry over the entire GET response.** Cherry-picking the "required" fields (`altId`, `altType`, `name`, `currency`, `invoiceItems`, `issueDate`, `dueDate`) is necessary but NOT sufficient: the `UpdateInvoiceDto` schema also rejects bodies that omit nested objects which exist on the live invoice (`businessDetails`, `contactDetails`, `discount`) with `"<field> should not be empty"` 422s. Spread `{ ...invoice }`, strip 9 server-managed meta fields (`_id, companyId, createdAt, updatedAt, __v, externalSources, lastVisitedAt, opportunityId, traceId`), override the field(s) you want, normalize dates, then PUT.
- Date format: `issueDate` and `dueDate` must be `YYYY-MM-DD` on PUT, but GET returns ISO 8601 timestamps. Strip with `.split('T')[0]` or you'll get a 422.
- No status gating: PUT works on a `paid` invoice — `status`, `amountPaid`, and payment history are all preserved.
- `invoiceId` must be the internal MongoDB `_id`, NOT the human-readable `invoiceNumber` (sending the number gives 422 `"invoiceId must be a mongodb id"`). Resolve via List Invoices `?search={number}` **with a client-side exact-match filter** on `invoiceNumber === requestedNumber` — `search` is loose and could match `1000123` for `000123`, silently editing the wrong invoice.
- `Version: 2023-02-21` is the docs-current version header; older `2021-07-28` still works, but prefer the current one.

**Custom Objects**
- `POST /objects/{schemaKey}/records/search`: use `pageLimit`, NOT `pageSize` (422, no soft fallback).
- `GET /objects/{schemaKey}/records/{id}`: the response uses `follower` (singular) where the search endpoint uses `followers` (plural). Both are arrays — parse defensively.
- `GET /objects/{key}?fetchProperties=true`: currency fields have dataType `MONETORY` (a GHL typo, not `monetary`). Value structure: `{"currency": "default", "value": 2240}`. Match on the misspelled string.
- `POST /objects/` (create custom object type) requires an agency-level token — a location-scope PIT returns 401.
