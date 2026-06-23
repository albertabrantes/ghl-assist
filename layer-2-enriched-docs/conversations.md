# Conversations API

Documentation for GoHighLevel Conversations endpoints — messaging, inbound message injection, and Conversation AI.

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

> **PARTIALLY VERIFIED:** Key endpoints were tested with live API calls. Look for `✅ VERIFIED` tags on individual sections. Unverified sections still exist -- test before relying on them.

---

## Overview

The Conversations API handles messaging between businesses and contacts in GHL. It covers:

- **Inbound messages** — recording messages from contacts (API injection or carrier delivery)
- **Outbound messages** — sending messages from the business to contacts
- **Message retrieval** — reading conversation history
- **Conversation search** — finding conversations by criteria
- **Conversation AI** — managing AI bots that auto-respond to inbound messages (separate API family, documented below)

There are two generations of inbound message endpoints:
- **V1** (`POST /conversations/messages/inbound`) — uses `type` field. ✅ VERIFIED working.
- **V2** (`POST /conversations/inbound-messages`) — ❌ **DOES NOT EXIST.** Returns 404. Was documented in GHL support articles but the endpoint is not live. Do not use.

> **Also see:** [voice-ai.md](voice-ai.md) for Search Conversations and the original Send Message (SMS) documentation used for Voice AI alerting.

## Required Scopes

| Scope | Access Level | Description |
|-------|--------------|-------------|
| `conversations.readonly` | Read | Search conversations |
| `conversations.write` | Write | Create conversations |
| `conversations/message.readonly` | Read | Read conversation messages |
| `conversations/message.write` | Write | Send and inject messages |
| `conversations/livechat.readonly` | Read | Read live chat data |
| `conversations/livechat.write` | Write | Manage live chat |

---

## Rate Limits

| Limit Type | Value |
|------------|-------|
| Burst Limit | 100 requests / 10 seconds / location |
| Daily Limit | 200,000 requests / day / location |

Rate limit headers are included in responses. Implement exponential backoff on `429` responses and respect the `Retry-After` header.

---

## Authentication

All requests require a Bearer token — either an OAuth 2.0 access token or a Private Integration Token (PIT) scoped to the sub-account.

```
Authorization: Bearer {access_token}
```

> **IMPORTANT:** Use a **Sub-Account level** token. Agency-level tokens return `403` on location-scoped conversation operations. Use a PIT from Settings > Private Integrations in the sub-account. [UNVERIFIED — from community reports]

---

## Endpoints Summary

### Inbound Messages

| Endpoint | Method | Description |
|----------|--------|-------------|
| [Add Inbound Message (V1)](#add-inbound-message-v1) | POST | Inject inbound message using `type` field |
| [Add Inbound Message (V2)](#add-inbound-message-v2) | POST | Inject inbound message using `channel` field with auto-threading |

### Outbound Messages

| Endpoint | Method | Description |
|----------|--------|-------------|
| [Send Outbound Message](#send-outbound-message) | POST | Send message from business to contact |

### Message Retrieval

| Endpoint | Method | Description |
|----------|--------|-------------|
| [Get Messages](#get-messages) | GET | Get messages in a conversation |

### Conversation Search

| Endpoint | Method | Description |
|----------|--------|-------------|
| [Search Conversations](#search-conversations) | GET | Search/filter conversations |

### Conversation AI API

| Endpoint | Method | Description |
|----------|--------|-------------|
| [Search AI Agents](#search-ai-agents) | GET | List Conversation AI agents |
| [Get AI Agent](#get-ai-agent) | GET | Get agent configuration |
| [Create AI Agent](#create-ai-agent) | POST | Create a new AI agent |
| [Update AI Agent](#update-ai-agent) | PUT | Update agent configuration |
| [Delete AI Agent](#delete-ai-agent) | DELETE | Delete an AI agent |
| [Get AI Generations](#get-ai-generations) | GET | Get AI response generation details |

---

## Add Inbound Message (V1)

Record an inbound message in a conversation as if the contact sent it. **Does not involve carrier delivery** — it only creates the message record in GHL's conversation thread.

> ✅ **VERIFIED:** Tested with live API calls. Both `type: "SMS"` and `type: "Live_Chat"` confirmed working. See verified response examples and behavior notes below.

**Endpoint:** `POST /conversations/messages/inbound`

**Required Scope:** `conversations/message.write`

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | string | Yes | Message type: `"SMS"`, `"Email"`, `"Custom"`, `"Call"` |
| `conversationId` | string | Yes* | Target conversation ID |
| `contactId` | string | Yes* | Target contact ID (alternative to conversationId) |
| `message` | string | Yes | Message text content (also accepts `body`) |
| `conversationProviderId` | string | Conditional | Required for custom providers; NOT required for default SMS/Email |
| `attachments` | array | No | Array of attachment URLs |
| `contentType` | string | No | e.g., `"text/plain"` |
| `altId` | string | No | For Custom-type providers; enables `replyToAltId` in outbound payloads |

*Either `conversationId` or `contactId` must be provided. If only `contactId` is given, GHL auto-resolves the conversation.

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-07-28
Content-Type: application/json
Accept: application/json
```

### Type Field Values

| `type` value | Provider context | `conversationProviderId` needed? | Triggers Bot? |
|---|---|---|---|
| `"SMS"` | Default SMS provider OR custom SMS channel | No for default; Yes for custom | ✅ Yes — bot responds via real SMS (Twilio) |
| `"Live_Chat"` | Live chat / web chat widget | No | ✅ Yes — bot responds via Live Chat (NO real SMS sent) |
| `"Email"` | Default Email provider | No | [UNVERIFIED] |
| `"Custom"` | Extra/custom Email or other provider | Yes (returns 400 without it) | [UNVERIFIED] |
| `"Call"` | Call logging provider | Yes | [UNVERIFIED] |

> **IMPORTANT DISCOVERY:** `type: "Live_Chat"` is the **recommended type for testing**. It triggers the Conversation AI bot just like SMS, but the bot response stays inside GHL — no real SMS is sent via Twilio. This avoids sending unsolicited texts to real phone numbers. Using `type: "SMS"` causes the bot to send **real SMS via Twilio** to the contact's phone number.
>
> **Note:** You cannot set `type: "WhatsApp"` or `type: "Facebook"` on this endpoint. Social channels are native GHL integrations. The V2 endpoint documented in GHL support articles **does not exist** (returns 404).

### Example Request

```bash
curl -X POST "https://services.leadconnectorhq.com/conversations/messages/inbound" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "type": "SMS",
    "contactId": "CONTACT_ID_HERE",
    "message": "Hello, I would like to book an appointment"
  }'
```

### Verified Response Example

For `type: "SMS"`:
```json
{
  "success": true,
  "conversationId": "EXAMPLEConvo00000001",
  "messageId": "EXAMPLEMessage000001",
  "traceId": "bda9a15c-9677-41a0-ba3a-71e3be280c24"
}
```

For `type: "Live_Chat"` (also includes `contactId` and `dateAdded`):
```json
{
  "success": true,
  "conversationId": "EXAMPLEConvo00000001",
  "messageId": "EXAMPLEMessage000003",
  "contactId": "EXAMPLEContact000001",
  "dateAdded": "2026-02-18T08:40:39.222Z",
  "traceId": "02918fcd-c93e-4211-b754-90094c575e04"
}
```

### Error Responses

| Status | Meaning |
|--------|---------|
| 200 | Message created successfully |
| 400 | Invalid contactId, missing type, malformed body. ✅ `type: "Custom"` without `conversationProviderId` returns `"conversationProviderId is required"` |
| 401/403 | Auth failed or insufficient scope. ✅ PIT needs `conversations/message.write` scope |
| 404 | Contact not found in location context |
| 409 | Idempotency key collision |
| 429 | Rate limit exceeded; respect `Retry-After` header |

### Important Notes

- **No carrier delivery on inbound:** This endpoint only records the message in GHL. It does not send an actual SMS or email to the contact.
- ✅ **Conversation AI triggering:** Messages injected with `type: "SMS"` or `type: "Live_Chat"` through the default provider path (no `conversationProviderId`) **DO trigger Conversation AI bots**. Bot response time is approximately 9-11 seconds.
- ✅ **SMS type sends REAL outbound SMS:** When the bot responds to a `type: "SMS"` message, the response is sent as a **real SMS via Twilio** to the contact's phone number. The bot response `altId` field contains a Twilio Message SID (format: `SM{hex}`). This means test contacts with random phone numbers will result in real people receiving AI-generated text messages. **Use `type: "Live_Chat"` for testing to avoid this.**
- ✅ **Live_Chat type is safe for testing:** `type: "Live_Chat"` triggers the bot identically, but bot responses are `TYPE_LIVE_CHAT` with no Twilio delivery. The response `altId` is empty/null, confirming no carrier delivery.
- ✅ **Bot response fields:** Bot responses have `source: "app"` (not `"conversation_ai"` as speculated), `direction: "outbound"`, and `messageType` matching the inbound type (e.g., `TYPE_SMS` or `TYPE_LIVE_CHAT`).
- **Workflow triggering:** SMS via default provider path supports standard SMS workflow modules including "Customer Replied" triggers. Custom providers do NOT support SMS workflow triggers. [CONFIRMED from official docs]
- ✅ **Bot sleep behavior:** The bot was observed as non-responsive when a contact had prior manual/bot conversation history on a different channel (Instagram). After the bot was manually re-enabled, it responded to injected messages. Manual messages DO put the bot to sleep.
- ✅ **Bot activation gating:** The Conversation AI bot may have IF conditions that gate activation (e.g., contact email must match a domain). Fresh contacts that don't match the condition will NOT receive bot responses, even though the inbound message is successfully injected.
- **"Cold inbound" caveat:** The "Customer Replied" workflow trigger reportedly does not fire for contacts who have never been messaged from GHL first. [UNVERIFIED — from community reports]

---

## Add Inbound Message (V2)

> ❌ **DOES NOT EXIST:** This endpoint returns **404 Not Found** (`"Cannot POST /conversations/inbound-messages"`). It was documented in a GHL support article but is NOT live on the API. Do not use. Use the [V1 endpoint](#add-inbound-message-v1) instead.

Newer contact-based variant with broader channel support and automatic thread resolution. Described in GHL support articles but not yet deployed.

**Endpoint:** `POST /conversations/inbound-messages`

**Required Scope:** `conversations/message.write`

### Request Body

```json
{
  "contactId": "CONTACT_ID",
  "channel": "sms",
  "endpoint": {
    "phone": "+15551234567",
    "email": "user@example.com"
  },
  "content": {
    "text": "Hello from the contact",
    "attachments": [
      {
        "type": "image",
        "url": "https://example.com/file.jpg",
        "filename": "file.jpg",
        "sizeBytes": 123456
      }
    ]
  },
  "metadata": {
    "providerMessageId": "ext-abc-123",
    "externalThreadKey": "optional-correlation",
    "timestamp": "2026-02-04T10:15:30Z"
  },
  "idempotencyKey": "fd2d5f6f-5a9f-4b0a-8d68-0d5f6a1c9e5a"
}
```

### Request Body Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `contactId` | string | Yes | Target contact ID |
| `channel` | string | Yes | Channel type (see values below) |
| `endpoint` | object | No | Disambiguation hint for contacts with multiple numbers/emails |
| `endpoint.phone` | string | No | Phone number to match (for SMS/WhatsApp) |
| `endpoint.email` | string | No | Email to match (for Email channel) |
| `content` | object | Yes | Message content |
| `content.text` | string | Yes | Message text |
| `content.attachments` | array | No | Array of attachment objects |
| `metadata` | object | No | Provider metadata |
| `metadata.providerMessageId` | string | No | External message ID for tracking |
| `metadata.externalThreadKey` | string | No | Correlation key for threading |
| `metadata.timestamp` | string | No | ISO 8601 timestamp of original message |
| `idempotencyKey` | string | Recommended | UUID to prevent duplicate messages on retry |

### Channel Values

| `channel` value | Description |
|-----------------|-------------|
| `"sms"` | SMS text message |
| `"whatsapp"` | WhatsApp message |
| `"email"` | Email message |
| `"messenger"` | Facebook Messenger |
| `"instagram"` | Instagram DM |
| `"webchat"` | Live chat / web chat widget |

> **Channel affects bot triggering:** The Conversation AI bot must be assigned to the specific channel in its settings. If the bot is assigned to SMS but not WhatsApp, a `channel: "whatsapp"` message will not trigger it. [UNVERIFIED]

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-07-28
Content-Type: application/json
Accept: application/json
```

### Example Request

```bash
curl -X POST "https://services.leadconnectorhq.com/conversations/inbound-messages" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -d '{
    "contactId": "CONTACT_ID_HERE",
    "channel": "sms",
    "content": {
      "text": "Hello, I would like to book an appointment"
    },
    "idempotencyKey": "test-msg-001"
  }'
```

### Response

```json
{
  "messageId": "MSG_123",
  "conversationId": "CONV_987",
  "contactId": "CONTACT_ID",
  "channel": "sms",
  "direction": "inbound",
  "createdAt": "2026-02-04T10:15:31Z"
}
```

> **UNVERIFIED:** This response schema is from documentation, not a live API call. The actual response may include additional fields. Update this when tested.

### Threading Behavior

The V2 endpoint handles thread resolution automatically based on `contactId`:

1. **Existing open conversation (same channel):** Message is appended to it
2. **No suitable thread:** A new conversation is created; `conversationId` returned in response
3. **Only closed/archived threads:** A new conversation is created
4. **Multiple phone numbers per contact:** Include `endpoint.phone` to disambiguate

You do NOT need to know the `conversationId` in advance. The response always includes the resolved `conversationId`.

> **One conversation per contact:** GHL scopes documentation states "one conversationId is maintained per contact," indicating a 1:1 mapping with all channel messages aggregated under a single conversation record. [UNVERIFIED — may vary by account configuration]

### Idempotency Key Behavior

Always include an `idempotencyKey` (UUID recommended). If you retry a request with the same key, GHL returns the original response without creating a duplicate. A `409 Conflict` status indicates an idempotency key collision.

### Error Responses

| Status | Meaning |
|--------|---------|
| 200 | Message created successfully |
| 400 | Invalid contactId, missing channel, malformed content |
| 401/403 | Auth failed or insufficient scope |
| 404 | Contact not found in location context |
| 409 | Idempotency key collision or duplicate |
| 413 | Attachments exceed size limits |
| 429 | Rate limit exceeded; respect `Retry-After` header |
| 5xx | Transient error; retry with same idempotencyKey |

---

## Send Outbound Message

Send a message FROM the business TO a contact. This physically delivers the message through the configured provider (Twilio, LC-Phone, Mailgun, etc.).

> **PARTIALLY VERIFIED:** The SMS type was tested in a Voice AI alerting context. Email and other types are from official docs. See also [voice-ai.md](voice-ai.md#send-message-sms) for the original SMS-only documentation.

**Endpoint:** `POST /conversations/messages`

**Required Scope:** `conversations/message.write`

### Request Body (SMS)

```json
{
  "type": "SMS",
  "contactId": "CONTACT_ID",
  "message": "Your appointment is confirmed for tomorrow at 2pm"
}
```

### Request Body (Email)

```json
{
  "type": "Email",
  "contactId": "CONTACT_ID",
  "html": "<p>Your appointment is confirmed</p>",
  "subject": "Appointment Confirmation",
  "emailFrom": "business@example.com",
  "emailTo": "contact@example.com"
}
```

### Supported Types

| `type` value | Additional fields | Notes |
|---|---|---|
| `"SMS"` | `message` | Standard text message |
| `"Email"` | `html`, `subject`, `emailFrom`, `emailTo`, `emailCc`, `emailBcc` | Full email support |
| `"WhatsApp"` | `message` | WhatsApp via connected number [UNVERIFIED] |
| `"Custom"` | Varies by provider | Custom channel provider [UNVERIFIED] |

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-07-28
Content-Type: application/json
Accept: application/json
```

> **Note on the Version header:** The official Conversations OpenAPI spec declares `Version: 2021-04-15` (the only enum value, marked required). In practice the live API accepts **both** `2021-04-15` and `2021-07-28` — both return `200` with data; an unrecognized or missing version returns `401`. Either dated version works; `2021-04-15` is what the spec mandates. **Tested** against a live location.

### Key Difference from Inbound Endpoint

The outbound endpoint **physically sends** the message through the carrier/provider. The inbound endpoint **only records** a message in the conversation — no carrier delivery occurs.

### Important Notes

- The `contactId` must be a GHL Contact ID (not a phone number)
- The contact must exist in GHL with a valid phone number or email
- SMS delivery typically occurs within 30 seconds
- **Bot sleep behavior:** Sending an outbound message to a contact will put the Conversation AI bot to sleep for that contact. [UNVERIFIED]

---

## Get Messages

Retrieve messages from a specific conversation thread. Use this to read conversation history, including bot-generated responses.

> ✅ **VERIFIED:** Tested with live API calls. Response structure confirmed below.

**Endpoint:** `GET /conversations/{conversationId}/messages`

**Required Scope:** `conversations/message.readonly` or `conversations.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `conversationId` | string | Yes | The conversation ID to retrieve messages from |

### Query Parameters

Standard GHL pagination applies. Expect `limit` and `offset` or cursor-based parameters.

> **Note:** The export variant (`GET /conversations/messages/export`) uses cursor-based pagination. The per-conversation endpoint's pagination parameters are not fully documented in the static API docs. [UNVERIFIED]

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-07-28
Accept: application/json
```

### Example Request

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/conversations/CONVERSATION_ID/messages" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Accept: application/json"
```

### Response

> ✅ **VERIFIED:** The wrapper structure is `{ messages: { messages: [...], lastMessageId, nextPage } }` — note the double-nested `messages`.

```json
{
  "messages": {
    "lastMessageId": "EXAMPLEMessage000000",
    "nextPage": true,
    "messages": [
      {
        "id": "EXAMPLEMessage000001",
        "direction": "inbound",
        "status": "delivered",
        "type": 2,
        "locationId": "EXAMPLELocation00000",
        "body": "Hi there, I am looking for cleaning services",
        "contactId": "EXAMPLEContact000001",
        "contentType": "text/plain",
        "conversationId": "EXAMPLEConvo00000001",
        "dateAdded": "2026-02-18T07:54:07.843Z",
        "dateUpdated": "2026-02-18T07:54:07.843Z",
        "from": "+15551234567",
        "to": "Acme Cleaning Co",
        "messageType": "TYPE_SMS"
      }
    ]
  },
  "traceId": "00000000-0000-0000-0000-000000000000"
}
```

> **Note:** Messages are returned most-recent-first. The `type` field is numeric (e.g., `2` for SMS, `29` for Live Chat, `18` for Instagram), while `messageType` is a readable string (`TYPE_SMS`, `TYPE_LIVE_CHAT`, `TYPE_INSTAGRAM`, etc.).

### Key Message Fields

| Field | Type | Description |
|-------|------|-------------|
| `direction` | string | `"inbound"` (from contact) or `"outbound"` (from business/bot) |
| `type` | string | `"InboundMessage"` or `"OutboundMessage"` |
| `messageType` | string | Channel: `"SMS"`, `"Email"`, etc. |
| `body` | string | Message text content |
| `dateAdded` | string | ISO 8601 timestamp |
| `source` | string | Origin — e.g., `"app"`. Bot messages may use `"conversation_ai"` or similar [UNVERIFIED] |
| `userId` | string | User who sent (null or system ID for bot messages) [UNVERIFIED] |
| `status` | string | Delivery status |

### Filtering

There is **no** server-side `direction` query parameter. Filter client-side using the `direction` field on each message.

### Bot-Generated Messages

> ✅ **VERIFIED**

Bot responses from Conversation AI **are included** in the message list. They appear as outbound messages with these characteristics:

| Field | Value | Notes |
|-------|-------|-------|
| `direction` | `"outbound"` | Same as manual outbound messages |
| `source` | `"app"` | NOT `"conversation_ai"` as speculated in prior docs |
| `messageType` | Matches inbound channel | `TYPE_SMS` if inbound was SMS, `TYPE_LIVE_CHAT` if inbound was Live Chat |
| `altId` | Twilio SID for SMS (`SM{hex}`), empty for Live Chat | Confirms whether real carrier delivery occurred |
| `userId` | Not present on bot messages | Manual messages have a `userId` field |
| `attachments` | `[]` (empty array) | Always present on bot messages |

Example bot response (SMS channel):
```json
{
  "id": "EXAMPLEMessage000004",
  "direction": "outbound",
  "status": "delivered",
  "type": 2,
  "body": "That's right, we do deep cleans for move-ins...",
  "source": "app",
  "altId": "SM57d24636edfa1b7df60ec5bbf61e3b44",
  "from": "+15551234500",
  "to": "+15551234567",
  "messageType": "TYPE_SMS"
}
```

Example bot response (Live Chat channel — no real SMS):
```json
{
  "id": "EXAMPLEMessage000002",
  "direction": "outbound",
  "status": "delivered",
  "type": 29,
  "body": "Hey! Can you let me know the full address...",
  "source": "app",
  "from": "Acme Cleaning Co",
  "to": "TestBot",
  "messageType": "TYPE_LIVE_CHAT"
}
```

### Important Notes

- ✅ **One conversation per contact:** GHL maintains a single `conversationId` per contact. All messages to the same contact (across multiple API calls) go to the same conversation. You do not need to track the conversation ID — it is auto-resolved from the `contactId`.
- **Two-step lookup:** If you only have a `contactId`, first use [Search Conversations](#search-conversations) to find the `conversationId`, then call this endpoint
- **Alternative:** If you used the [V2 inbound endpoint](#add-inbound-message-v2), the response includes `conversationId` — use that directly
- ⚠️ **BUG WARNING:** `GET /conversations/messages?type=VOICE` does NOT work — GHL interprets `messages` as a `conversationId` path parameter, returning `400 "Conversation with id messages not found"`. Always include a real conversation ID in the path.

---

## Search Conversations

Search and filter conversations for a location.

> **See also:** [voice-ai.md](voice-ai.md#search-conversations-alternative-for-call-logs) for the original documentation of this endpoint.

**Endpoint:** `GET /conversations/search`

**Required Scope:** `conversations.readonly`

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `query` | string | No | Search text/keyword |
| `contactId` | string | No | Filter by specific contact |
| `status` | string | No | Conversation status: `all`, `read`, `unread`, `starred` |
| `lastMessageType` | string | No | Filter by last message type (e.g., `TYPE_SMS`, `TYPE_CALL`, `TYPE_EMAIL`) |
| `limit` | number | No | Results per page |
| `startAfterDate` | string | No | Date cursor for pagination |
| `sort` | string | No | Sort field |
| `sortDirection` | string | No | `asc` or `desc` |

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-07-28
Accept: application/json
```

### Example Request

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/conversations/search?locationId=LOCATION_ID&contactId=CONTACT_ID" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Accept: application/json"
```

### Important Notes

- The `lastMessageType` filter only checks the **last** message in each conversation, not all messages
- Use this to find `conversationId` for a given `contactId` before calling [Get Messages](#get-messages)
- ✅ **VERIFIED:** This endpoint works (tested in a Voice AI context)

---

## Conversation AI API

The Conversation AI API is a **separate API family** (path prefix `/conversation-ai/`) for managing AI bots that auto-respond to inbound messages. It includes 12 endpoints across 3 families: Agents, Actions, and Generations.

> **UNVERIFIED:** This entire section is from official GHL docs and support articles. None of these endpoints have been tested with live API calls.

### Conversation AI — How It Works

- The **primary bot** (`isPrimary: true`) handles all inbound messages not initiated within a workflow
- The bot monitors **assigned channels** (SMS, Facebook, Instagram, live chat, etc.)
- Each contact has an independent bot status: **Active** (bot responds), **Sleep/Snooze** (temporarily paused), or **Inactive** (permanently off until reactivated)
- The bot has a **configurable "Wait Time Before Responding"** (default 10 seconds, max 300 seconds) — it collects multiple back-to-back messages before responding
- ✅ Expected response time: **approximately 9-11 seconds** in testing. First response after bot wake-up may be slower (~60-90 seconds).

### Per-Contact Bot Status

| Status | Behavior |
|--------|----------|
| **Active** | Bot responds to inbound messages |
| **Sleep/Snooze** | Temporarily paused; can auto-wake after timer expires |
| **Inactive** | Permanently off; requires manual or workflow reactivation |

- Contact-level **Inactive overrides** global bot settings
- Bot automatically goes to **Sleep** when a manual or workflow message is sent to the contact
- Bot goes to **Sleep** when max message limit is reached
- Reset via the "Update Conversation AI Bot and Status" workflow action

[UNVERIFIED — from official Conversation AI documentation]

---

### Search AI Agents

List Conversation AI agents for a location.

**Endpoint:** `GET /conversation-ai/agents/search`

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location ID |

### Get AI Agent

Get a single Conversation AI agent's configuration.

**Endpoint:** `GET /conversation-ai/agents/{agentId}`

**Response includes:** `mode` (autopilot/suggestive/off), assigned `channels`, `personality`, `goal`, `name`, `isPrimary` flag.

### Create AI Agent

**Endpoint:** `POST /conversation-ai/agents`

### Update AI Agent

**Endpoint:** `PUT /conversation-ai/agents/{agentId}`

Can update mode, channels, personality, goal, name, and other settings.

### Delete AI Agent

**Endpoint:** `DELETE /conversation-ai/agents/{agentId}`

---

### Conversation AI Actions

Actions are behaviors attached to AI agents (e.g., book appointment, collect info, hand off to human).

| Endpoint | Method | Description |
|----------|--------|-------------|
| `POST /conversation-ai/agents/{agentId}/actions` | POST | Attach action to agent |
| `GET /conversation-ai/agents/{agentId}/actions` | GET | List actions on agent |
| `GET /conversation-ai/agents/{agentId}/actions/{actionId}` | GET | Get specific action |
| `PUT /conversation-ai/agents/{agentId}/actions/{actionId}` | PUT | Update action |
| `DELETE /conversation-ai/agents/{agentId}/actions/{actionId}` | DELETE | Remove action |
| `PUT /conversation-ai/agents/{agentId}/followup-settings` | PUT | Update follow-up settings |

**Action types include:** appointment booking, contact info collection, workflow triggering, human handover, auto follow-up, stop bot, transfer to another bot. [UNVERIFIED]

---

### Get AI Generations

Retrieve detailed information about each AI response, including the system prompt used, conversation history, knowledge base chunks referenced, and reasoning. This is the richest data source for debugging and testing bot behavior.

**Endpoint:** `GET /conversation-ai/generations`

**Use case:** After injecting a test inbound message and waiting for the bot to respond, call this endpoint to inspect exactly how the bot processed the message — what context it used, which knowledge base chunks were retrieved, and the full reasoning chain.

> **PARTIALLY VERIFIED:** This endpoint requires `messageId` and `source` as required parameters (not `locationId` or `contactId`). Calling without them returns 422: `"messageId must be a string"`, `"messageId should not be empty"`, `"source should not be empty"`, `"source must be a valid enum value"`. Full parameter discovery still needed.

---

## Conversation Providers

> **This section is reference material** explaining how GHL's provider system works. It affects whether API-injected messages trigger bots and workflows.

A **Conversation Provider** is a GHL Marketplace module that creates custom SMS, Email, or Call providers. There are six types:

| Provider Type | Workflow Triggers | SMS Modules |
|---|---|---|
| SMS (default replacement) | ✅ Supported | ✅ Supported |
| SMS (custom channel) | ❌ Not supported | ❌ Not supported |
| Email (default replacement) | ⚠️ Triggers unsupported | ✅ Modules supported |
| Email (custom channel) | ❌ Not supported | ❌ Not supported |
| Call provider | Varies | N/A |
| Custom | Varies | N/A |

**Key takeaway for API-injected messages:** Using `type: "SMS"` with NO `conversationProviderId` routes through the default SMS provider path, which has full workflow trigger and module support. This is the recommended approach for testing bot triggering. [CONFIRMED — from official Conversation Providers documentation]

**Marketplace provider registration** gives you a `conversationProviderId`. You do NOT need this for default SMS/Email — only for custom providers.

---

## Gotchas & Known Issues

> **Collected from community reports and official docs. Mark as VERIFIED when you confirm them.**

1. **Base URL matters:** Always use `https://services.leadconnectorhq.com`. Using `rest.gohighlevel.com` or `api.gohighlevel.com` returns 404. [UNVERIFIED — from community reports]

2. **Token scope must be Sub-Account level.** Agency tokens return 403 on location-scoped operations. [UNVERIFIED — from community]

3. **Contact ID must match token's location.** Cross-location contact IDs return 404. A `400 "Contact not found for id"` means the contact isn't in the token's location scope. [UNVERIFIED — from community reports]

4. **No outbound message logging for custom providers.** If using a custom conversation provider, there is no API to inject outbound messages — only inbound. [UNVERIFIED — from ideas.gohighlevel.com]

5. **Message status updates are provider-locked.** Only the marketplace app token that owns the conversation provider can update message statuses. Other installed apps cannot. [UNVERIFIED — from community]

6. **Bot sleep on manual message.** Any manual (non-bot) outbound message puts the Conversation AI bot to sleep for that contact. This includes messages sent during debugging. [UNVERIFIED]

7. **WhatsApp coexistence bug.** Messages from alternative WhatsApp apps don't always interact correctly with bot sleep behavior. [UNVERIFIED — from community]

8. ✅ **GHL lowercases contact names.** When creating contacts via API, GHL lowercases `firstName` and `lastName`. For example, `TestBot` becomes `testbot`. Case-insensitive matching is required when searching for contacts by name.

9. ✅ **Contacts API requires `contacts.write` scope.** Creating contacts returns 401 without the `contacts.write` scope on the PIT.

10. ✅ **Upsert returns existing contact if duplicate.** The `POST /contacts/upsert` endpoint returns the existing contact ID when a duplicate phone or email is detected, rather than creating a new contact. The location also has duplicate contact prevention that rejects `POST /contacts/` with a matching phone number.

11. **Email-tag contacts for safe testing.** Contacts created with only an email (no phone number) using a plus-tag format like `user+{tag}@example.com` trigger a bot when the bot's IF condition matches `@example.com`. This is a useful pattern for automated testing — email-tagged contacts give isolated conversations per test without any SMS delivery risk.

---

## Related Documentation

- [Voice AI API](voice-ai.md) — Voice AI agent config, call logs, and the original Send Message (SMS) docs
- [Contacts API](contacts/README.md) — Contact CRUD (needed to create test contacts)
- [Custom Fields API](custom-fields/README.md) — Managing custom fields (e.g., Contact Stage)

---

## Community Resources

These open-source projects implement GHL conversation APIs and may be useful references:

- **ampilares/selfhostsim** — Self-hosted SMS gateway for GHL using Android phones. Implements inbound message API flow with NestJS.
- **green-api/greenapi-integration-gohighlevel** — WhatsApp-GHL bridge via Conversation Provider. NestJS + Docker.
- **M2KDevelopments/gohighlevel-py** — Python wrapper with `ghl.conversations.messages.add()` method.
- **mastanley13/GoHighLevel-MCP** — 269+ GHL tools via Model Context Protocol, including 20 conversation tools.

[UNVERIFIED — these repos were identified in research but not tested]

---

> **Agent Note:** If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:** {date}` note to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.
