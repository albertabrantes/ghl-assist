# Voice AI API

Documentation for GoHighLevel Voice AI endpoints.

**Base URL:** `https://services.leadconnectorhq.com`
**Version Header:** `Version: 2021-04-15`
**Required Scopes:** `voice-ai-agents.readonly`, `voice-ai-agents.write`

---

## List Call Logs

Retrieve a paginated list of Voice AI call logs for a location. Supports filtering by agent, contact, call type, action types, and date range with timezone-aware interpretation.

**Endpoint:** `GET /voice-ai/dashboard/call-logs`

**Required Scope:** `voice-ai-dashboard.readonly` (NOT `voice-ai-agents.readonly`)

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `agentId` | string | No | Filter by specific Voice AI agent ID |
| `contactId` | string | No | Filter by specific GHL contact ID |
| `callType` | string | No | Filter by call type: `LIVE` or `TRIAL` |
| `actionTypes` | string | No | Filter by action types triggered during calls |
| `startDate` | number | No | Unix timestamp in milliseconds. Must be less than `endDate`. Both `startDate` and `endDate` must be provided together. Example: `1679308800000` |
| `endDate` | number | No | Unix timestamp in milliseconds. Must be greater than `startDate`. Both `startDate` and `endDate` must be provided together. Example: `1679395199000` |
| `timezone` | string | No | IANA timezone for date interpretation (e.g., `America/Los_Angeles`) |
| `page` | number | No | Page number (1-based pagination) |
| `pageSize` | number | No | Results per page (⚠️ NOT `limit` - will cause 422 errors!) |
| `sortBy` | string | No | Sort field (`duration` or `createdAt`) |
| `sort` | string | No | Sort direction (`ascend` or `descend`) |

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-04-15
Accept: application/json
```

### Example Request

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/voice-ai/dashboard/call-logs?locationId=abc123&page=1&pageSize=50&timezone=America/Los_Angeles" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-04-15" \
  -H "Accept: application/json"
```

### Example with Date Filtering

> **IMPORTANT:** `startDate` and `endDate` are **Unix timestamps in milliseconds** (type `number`), NOT ISO 8601 strings. Both must be provided together.

```bash
# Filter calls from March 20, 2023 00:00:00 UTC to March 21, 2023 23:59:59 UTC
curl -X GET \
  "https://services.leadconnectorhq.com/voice-ai/dashboard/call-logs?locationId=abc123&startDate=1679308800000&endDate=1679395199000&timezone=America/Los_Angeles&page=1&pageSize=50" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-04-15" \
  -H "Accept: application/json"
```

### Example with Agent Filter

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/voice-ai/dashboard/call-logs?locationId=abc123&agentId=agent123&page=1&pageSize=50" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-04-15" \
  -H "Accept: application/json"
```

### Response

Returns a paginated list of call log objects.

**Response Format:**

```json
{
  "callLogs": [
    {
      "id": "call-123",
      "callId": "call-123",
      "contactId": "contact-456",
      "callerNumber": "+15551234567",
      "duration": 120,
      "createdAt": "2026-02-12T14:30:00Z",
      "transcript": [
        {
          "speaker": "caller",
          "message": "Hello",
          "timestamp": 1000
        }
      ],
      "summary": "Customer inquiry"
    }
  ],
  "total": 100,
  "page": 1,
  "pageSize": 50
}
```

**Key Field:** Call logs array is returned in the **`callLogs`** field (NOT `data`).

**Pagination:**
- Use `total` and `pageSize` to calculate total pages: `Math.ceil(total / pageSize)`
- Increment `page` parameter to fetch next page (1-based)

**Notes:**
- ⚠️ **CRITICAL:** Parameter is `pageSize`, not `limit` (using `limit` causes 422 error)
- Response field is `callLogs`, not `data` (despite documentation suggesting otherwise)
- Total count available in `total` field for accurate pagination
- ⚠️ **SCOPE:** Requires `voice-ai-dashboard.readonly` (NOT `voice-ai-agents.readonly`)
- ⚠️ **PARAMETER ERROR:** Using `limit` instead of `pageSize` returns HTTP 422 "property limit should not exist"
- ✅ **VERIFIED:** This endpoint works and returns calls in the `callLogs` field

### Important Notes

- **Pagination is 1-based** (first page is `page=1`)
- **Date filtering requires Unix timestamps in milliseconds** — `startDate` and `endDate` are type `number`, NOT strings. Both must be provided together. Using ISO 8601 strings will silently fail (returns all results unfiltered).
- **Date range is timezone-aware** — the `timezone` parameter determines how date boundaries are interpreted for display/grouping
- **This is the correct endpoint for listing Voice AI calls** — do NOT use `GET /conversations/messages` which expects a `conversationId` path parameter
- **Rate limit:** Standard GHL rate limits apply (100 requests / 10 seconds)

### Date Parameter Reference

| Parameter | Type | Format | Example | Notes |
|-----------|------|--------|---------|-------|
| `startDate` | number | Unix ms | `1679308800000` | Must be < endDate. Both required together |
| `endDate` | number | Unix ms | `1679395199000` | Must be > startDate. Both required together |

**Converting dates to Unix timestamps (JavaScript):**
```javascript
// For a specific date
const startDate = new Date('2026-02-14T00:00:00Z').getTime() // 1771027200000
const endDate = new Date('2026-02-15T00:00:00Z').getTime()   // 1771113600000

// WRONG - Do NOT use ISO strings (will silently fail):
// startDate=2026-02-14T00:00:00Z  ← Returns all results, ignores filter
```

### Common Mistakes

> **BUG WARNING:** `GET /conversations/messages?type=VOICE` does NOT work for listing calls. GHL interprets `messages` as a `conversationId` path parameter, returning 400 "Conversation with id messages not found". Use this endpoint (`/voice-ai/dashboard/call-logs`) instead.

---

## Get Call Log

Retrieve a single Voice AI call log by its ID.

**Endpoint:** `GET /voice-ai/dashboard/call-logs/{callId}`

**Required Scope:** `voice-ai-agents.readonly`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `callId` | string | Yes | The call log ID (may correspond to `messageId` in conversation context) |

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-04-15
Accept: application/json
```

### Example Request

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/voice-ai/dashboard/call-logs/call123" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-04-15" \
  -H "Accept: application/json"
```

### Response

Returns the full call log object including transcript, summary, duration, contact info, and metadata.

### Important Notes

- The `callId` used in this endpoint may be the same as `messageId` used in webhook payloads — verify by testing
- Returns null/404 if the call ID is not found

---

## Voice AI Agent Configuration

### Get Agent Config

Retrieve Voice AI agent configuration.

**Endpoint:** `GET /voice-ai/agents/{agentId}?locationId={locationId}`

**Required Scope:** `voice-ai-agents.readonly`

> **Note:** The path is **plural** (`/voice-ai/agents/`), and the `locationId` query parameter is **required**. The singular path `/voice-ai/agent/{agentId}` returns 404, and omitting `locationId` returns 403 "LocationId is required". The prompt field in the GET response is `agentPrompt` (not `prompt`).

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `agentId` | string | Yes | The Voice AI agent ID |

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID (403 without it) |

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-04-15
Accept: application/json
```

---

### Update Agent Prompt (PATCH)

Update the Voice AI agent's system prompt.

**Endpoint:** `PATCH /voice-ai/agent/{agentId}`

**Required Scope:** `voice-ai-agents.write`

### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `agentId` | string | Yes | The Voice AI agent ID |

### Request Body

```json
{
  "prompt": "Updated system prompt text..."
}
```

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-04-15
Content-Type: application/json
Accept: application/json
```

### Important Notes

- After patching, verify the update by calling `GET /voice-ai/agent/{agentId}` and comparing the prompt
- Allow 2-3 seconds propagation delay before verification
- A small length variance (up to ~100 chars) may exist between sent and retrieved prompts due to GHL processing

---

## Conversations API (for SMS via Voice AI alerting)

> **Comprehensive Conversations API documentation** is now available in [conversations.md](conversations.md). That file covers inbound message injection (V1 & V2), outbound messaging, message retrieval, Conversation AI agents, and Conversation Providers. The docs below are retained for backward compatibility and Voice AI-specific context.

### Send Message (SMS)

Send an SMS message via the Conversations API. Used for Voice AI alerting (e.g., scheduler gap detection, update failure alerts).

**Endpoint:** `POST /conversations/messages`

**Required Scope:** `conversations/message.write`

**Version Header:** `Version: 2021-04-15`

### Request Body

```json
{
  "type": "SMS",
  "contactId": "ghl_contact_id_here",
  "message": "Message text here"
}
```

### Headers

```
Authorization: Bearer {access_token}
Version: 2021-04-15
Content-Type: application/json
Accept: application/json
```

### Important Notes

- The `contactId` is a **GHL Contact ID** (not a phone number)
- The contact must already exist in GHL with a valid phone number
- SMS delivery should occur within 30 seconds per typical SLA
- If the API fails, log the error but do not block calling operations (non-critical for scheduler)

---

## Search Conversations (Alternative for Call Logs)

> **Also documented in:** [conversations.md](conversations.md#search-conversations) with additional context on conversation threading and contact-to-conversation mapping.

If the Voice AI Call Logs endpoint doesn't return the data format needed, the Search Conversations API can be used as a fallback.

**Endpoint:** `GET /conversations/search`

**Required Scope:** `conversations.readonly`

**Version Header:** `Version: 2021-07-28` (note: different from Voice AI endpoints)

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `query` | string | No | Search text/keyword |
| `contactId` | string | No | Filter by specific contact |
| `status` | string | No | Conversation status (`all`, `read`, `unread`, `starred`) |
| `lastMessageType` | string | No | Filter by last message type (e.g., `TYPE_CALL`, `TYPE_SMS`) |
| `limit` | number | No | Results per page |
| `startAfterDate` | string | No | Date cursor for pagination |
| `sort` | string | No | Sort field |
| `sortDirection` | string | No | `asc` or `desc` |

### Example Request

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/conversations/search?locationId=abc123&lastMessageType=TYPE_CALL&limit=50" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Accept: application/json"
```

### Message Type Values

| Type | String | Description |
|------|--------|-------------|
| SMS | `TYPE_SMS` | Text messages |
| Call | `TYPE_CALL` | Voice calls |
| Email | `TYPE_EMAIL` | Email messages |
| Voicemail | `TYPE_VOICEMAIL` | Voicemail messages |

### Important Notes

- **Uses `Version: 2021-07-28`** — different from the Voice AI endpoints which use `2021-04-15`
- This is a two-step process: search conversations first, then get messages per conversation via `GET /conversations/{conversationId}/messages`
- **Prefer the Voice AI Call Logs API** (`GET /voice-ai/dashboard/call-logs`) for Voice AI specific call data — it's purpose-built and single-step
- The `lastMessageType` filter only checks the LAST message in each conversation, not all messages

---

## API Scopes Reference

| Scope | Permission | Purpose |
|-------|-----------|---------|
| `voice-ai-agents.readonly` | Read | Read Voice AI agent config and call logs |
| `voice-ai-agents.write` | Write | Patch agent prompt |
| `voice-ai-agent-goals.readonly` | Read | Read agent goal configuration |
| `voice-ai-agent-goals.write` | Write | Update agent goals |
| `conversations.readonly` | Read | Search conversations |
| `conversations.write` | Write | Create conversations |
| `conversations/message.readonly` | Read | Read conversation messages |
| `conversations/message.write` | Write | Send SMS/messages |

---

> **Agent Note:** If you use any endpoint documented here and find errors, missing fields, or wrong schemas, you are expected to fix this file and update the [master index](../index.md) — including its Known Issues Log. Add a `> **Tested:**` marker to any endpoint you verify with a live API call. See the Agent Instructions section in the index for full details.
