# File Uploads API Guide

**Base URL:** `https://services.leadconnectorhq.com`

> **Tested:** All findings verified with live API calls against a Private Integration Token.

## Overview

GHL has multiple ways to upload and attach files, each with different capabilities and limitations. This guide covers all tested approaches and documents what works, what doesn't, and what to use for each scenario.

## Quick Decision Table

| Goal | Approach | Endpoint | Works? | Size Limit |
|------|----------|----------|--------|------------|
| Upload file to contact Documents tab | Contact custom field (FILE_UPLOAD) | `POST /forms/upload-custom-files` | Yes | 50 MB |
| Upload file to opportunity custom field | Two-step: upload to contact first, then set URL on opp | `POST /forms/upload-custom-files` then `PUT /opportunities/{id}` | Yes | 50 MB |
| Upload file to media library | Media upload | `POST /medias/upload-file` | Yes | 25 MB |
| Upload file to conversation (for message attachment) | Conversation upload | `POST /conversations/messages/upload` | Yes | 5 MB |
| Upload file directly to opportunity Documents tab | N/A | N/A | No, opportunities don't have a Documents tab | N/A |
| Upload to contact Documents tab without a custom field | N/A | N/A | No, there is no direct Documents tab API | N/A |

---

## Method 1: Contact Custom Field Upload (RECOMMENDED for Contact Documents)

This is the best way to get files into a contact's **Documents tab** (under Internal > Custom Fields). Requires a FILE_UPLOAD type custom field on the contact model.

**Method:** `POST`
**URL:** `/forms/upload-custom-files`
**Scope:** `forms.write`
**Content-Type:** `multipart/form-data`
**API Version:** `2021-07-28`

Despite the `/forms/` path, this endpoint has nothing to do with forms. It uploads files directly to a contact's custom field.

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `contactId` | string | Yes | Contact ID to upload the file to |
| `locationId` | string | Yes | Location/sub-account ID |

### Request Body (multipart/form-data)

The form field key must follow the format: `{custom_field_id}_{uuid}`

Where `custom_field_id` is the ID of a FILE_UPLOAD type custom field, and `uuid` is any randomly generated UUID.

### Constraints

- **Max file size:** 50 MB
- **Allowed types:** PDF, DOCX, DOC, JPG, JPEG, PNG, GIF, CSV, XLSX, XLS, MP4, MPEG, ZIP, RAR, TXT, SVG
- Multiple files supported via multiple form-data fields

### curl Example

```bash
curl -X POST \
  "https://services.leadconnectorhq.com/forms/upload-custom-files?contactId=EXAMPLEContact000001&locationId=EXAMPLELocation00001" \
  -H "Authorization: Bearer $GHL_API_TOKEN" \
  -H "Version: 2021-07-28" \
  -F "EXAMPLECustomField01_$(uuidgen)=@/path/to/document.pdf"
```

### Response

```json
{
  "succeded": true,
  "contact": {
    "id": "EXAMPLEContact000001",
    "customFields": {
      "EXAMPLECustomField01": {
        "<uuid>": {
          "meta": {
            "fieldname": "EXAMPLECustomField01",
            "originalname": "document.pdf",
            "encoding": "7bit",
            "mimetype": "application/pdf",
            "size": 4070640,
            "uuid": "<uuid>"
          },
          "url": "https://services.leadconnectorhq.com/documents/download/<documentId>",
          "documentId": "<documentId>"
        }
      }
    }
  }
}
```

Note: GHL misspells "succeeded" as `"succeded"` in the response.

### Behavior: Multiple Uploads to Same Field

- **Custom field value:** Only stores the latest file reference (overwrites previous)
- **Documents tab:** Keeps ALL files ever uploaded. Files accumulate in the contact's Documents tab under Internal > Custom Fields, even after the field value is overwritten.

This is actually ideal for versioning. Upload new versions to the same field; the Documents tab retains the full history while the field always points to the latest.

### Where Files Appear in GHL UI

Contact record > Documents tab > Internal > Custom Fields folder

> **Tested:** Verified with live API. Both single and multiple uploads confirmed working. Documents tab accumulation behavior confirmed.

---

## Method 2: Opportunity Custom Field Upload (Two-Step)

There is NO direct file upload endpoint for opportunities. The `/forms/upload-custom-files` endpoint explicitly rejects `opportunityId`:

- Without `contactId`: returns `422 "contactId can't be empty"`
- With `opportunityId` param: returns `422 "property opportunityId should not exist"`

### Workaround: Upload to Contact, Set URL on Opportunity

**Step 1:** Upload the file to a contact using Method 1 (above). Extract the URL from the response.

**Step 2:** Set the opportunity's FILE_UPLOAD custom field via the Update Opportunity endpoint:

```bash
curl -X PUT \
  "https://services.leadconnectorhq.com/opportunities/{opportunityId}" \
  -H "Authorization: Bearer $GHL_API_TOKEN" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -d '{
    "customFields": [
      {
        "id": "EXAMPLEFile00000001",
        "value": "https://link.example.com/path/to/uploaded-file.pdf"
      }
    ]
  }'
```

GHL fetches the URL and stores it as a file attachment on the opportunity custom field.

### Opportunity FILE_UPLOAD Limitations

- **Overwriting does not work:** Sending a second URL to the same field is silently ignored. The field keeps the original file. The API returns 200 success, but the value does not change.
- **No Documents tab:** Opportunities do not have a Documents tab like contacts do. Files only appear as custom field values on the opportunity record.

> **Tested:** First upload works. Second upload to same field silently fails (returns 200 but value unchanged). Confirmed with GET after PUT.

---

## Method 3: Conversation Message Upload

Uploads files as conversation message attachments. These appear in the contact's conversation thread and may show in the Documents tab under Sent/Received depending on how the message is sent.

**Method:** `POST`
**URL:** `/conversations/messages/upload`
**Scope:** `conversations/message.write`
**Content-Type:** `multipart/form-data`
**API Version:** `2021-04-15` (note: different version header from other endpoints)

### Form Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `conversationId` | string | One of these required | Conversation ID |
| `contactId` | string | One of these required | Contact ID (alternative to conversationId) |
| `locationId` | string | Yes | Location ID |
| `fileAttachment` | binary | Yes | The file buffer |

You can use either `conversationId` or `contactId`. Using `contactId` uploads the file associated with the contact without needing to look up the conversation first.

### Constraints

- **Max file size:** 5 MB per file
- **Max files per request:** 5
- **Allowed types:** JPG, PNG, GIF, SVG, HEIC, AI, MP4, MPEG, 3GP, MP3, WAV, AAC, M4A, PDF, DOCX, XLS, PPT, ZIP, RAR, VCF, ICS

### curl Example (with contactId)

```bash
curl -X POST \
  "https://services.leadconnectorhq.com/conversations/messages/upload" \
  -H "Authorization: Bearer $GHL_API_TOKEN" \
  -H "Version: 2021-04-15" \
  -F "contactId=EXAMPLEContact000001" \
  -F "locationId=EXAMPLELocation00001" \
  -F "fileAttachment=@/path/to/document.pdf"
```

### Response

```json
{
  "uploadedFiles": {
    "document.pdf": "https://link.example.com/conversations-assets/location/{locationId}/conversations/contact/{contactId}/{uuid}.pdf"
  }
}
```

### Important: Upload Only Stages the File

Uploading via this endpoint only stages the file and returns a URL. It does NOT create a message or a Documents tab entry by itself. To make the file visible:

- **As a conversation message:** Use `POST /conversations/messages` with the returned URL in the `attachments` array. This will appear in the conversation thread AND the Documents tab.
- **As a hosted URL:** Use the returned URL in an opportunity custom field update (Method 2) or anywhere else a URL is needed.

### Gotcha: Sending Messages

When sending via `POST /conversations/messages`:
- `type: "SMS"` actually sends an SMS to the contact's phone
- `type: "Email"` actually sends an email
- `type: "Custom"` requires a `conversationProviderId` (returns 400 without it)

There is no clean "internal only" message type via the send message endpoint. The inbound message endpoint (`POST /conversations/messages/inbound`) can inject messages that appear as received, but these show as fake inbound messages in the conversation.

> **Tested:** Upload with both conversationId and contactId confirmed working. 5MB limit confirmed (larger files return 413). The upload-only behavior (no automatic Documents tab entry) confirmed.

---

## Method 4: Media Library Upload

Uploads files to the location's media library. Files are location-level assets with no contact or opportunity association.

See [medias.md](medias.md) for full documentation.

**Key facts:**
- **Endpoint:** `POST /medias/upload-file`
- **Max file size:** 25 MB
- **Scope:** `medias.write`
- **Supports folders:** Use `parentId` to organize into folders
- **No contact association:** Files are location-level only. No way to link a media library file to a specific contact or opportunity.

### When to Use Media Library

- Storing shared assets (logos, templates, marketing materials)
- Files that aren't contact-specific
- When you need a publicly accessible URL for a file

### When NOT to Use Media Library

- When you want files associated with a specific contact (use Method 1 instead)
- When you want files visible on a contact's Documents tab (use Method 1 instead)

---

## Things That Do NOT Work

These are confirmed non-functional.

### 1. Direct Documents Tab API
There is no API endpoint to upload directly to the contact Documents tab. The "Add documents" dialog in the GHL UI (which supports up to 250MB and has Internal/Sent/Received sections) uses an internal API that is not exposed publicly. Feature request has been open since November 2022 with 27+ votes.

### 2. Opportunity File Upload Endpoint
`POST /forms/upload-custom-files` does not accept `opportunityId`:
- `?opportunityId=xxx` without contactId: `422 "contactId can't be empty"`
- `?contactId=xxx&opportunityId=xxx`: `422 "property opportunityId should not exist"`
- Using an opportunity custom field ID with a contactId: returns success but file goes to contact, not opportunity

### 3. Overwriting Opportunity FILE_UPLOAD Fields
`PUT /opportunities/{id}` with a new URL for an existing FILE_UPLOAD field returns 200 but silently ignores the update. The original file value persists. Only the first write sticks.

### 4. Conversation Upload Creating Documents Tab Entries
`POST /conversations/messages/upload` (with contactId) returns a success response and a URL, but does NOT create an entry in the contact's Documents tab. The file is only staged for use in messages.

---

## Required Scopes Summary

| Method | Required Scope |
|--------|---------------|
| Contact custom field upload | `forms.write` |
| Opportunity custom field (update) | `opportunities.write` |
| Conversation message upload | `conversations/message.write` |
| Media library upload | `medias.write` |
| Search conversations (to find conversationId) | `conversations.readonly` |

---

## Recommended Approach for Document Storage

**For contact-level documents (Color Schedules, proposals, contracts):**
1. Create a FILE_UPLOAD custom field on the contact model (one-time setup)
2. Upload via `POST /forms/upload-custom-files` with `contactId` and `locationId`
3. File appears in contact's Documents tab under Internal > Custom Fields
4. Multiple uploads accumulate in Documents tab; field value always shows latest

**For opportunity-level documents:**
1. Create a FILE_UPLOAD custom field on the opportunity model (one-time setup)
2. Upload to contact first via Method 1 to get a hosted URL
3. Set the URL on the opportunity via `PUT /opportunities/{id}` with `customFields`
4. Note: opportunity fields cannot be overwritten after first write

**For large files (over 50MB):**
No API option exists. The GHL UI supports up to 250MB via the Documents tab "Add" button, but this is not exposed via API.
