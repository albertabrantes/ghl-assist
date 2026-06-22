---
name: ghl-assist
description: GoHighLevel (GHL) API expert providing endpoint documentation, request/response schemas, authentication guidance, and implementation examples. This skill should be used when working with GHL/GoHighLevel API integrations, building n8n workflows that call GHL, writing code that interacts with GHL endpoints, or needing to understand GHL API capabilities, scopes, rate limits, or data structures.
---

# GHL API Knowledge Base

## Overview

This skill provides comprehensive knowledge about the GoHighLevel (GHL) API. Documentation is maintained in a two-layer system: the official GHL OpenAPI specs (cloned locally) plus your own enriched endpoint docs.

## Knowledge Base Location

Point this at a local knowledge-base directory you maintain. Throughout this skill, `<knowledge-base>/ghl/` refers to that location.

```
<knowledge-base>/ghl/
```

### Two-Layer Documentation System

**Layer 1: Official OpenAPI Specs (source of truth)**
```
<knowledge-base>/ghl/highlevel-api-docs/apps/*.json
```
Cloned from `https://github.com/GoHighLevel/highlevel-api-docs`. Contains machine-readable OpenAPI/JSON specs for every GHL API domain. Update by running `git pull` inside the directory.

**Layer 2: Enriched Agent Docs (your additions)**
```
<knowledge-base>/ghl/api/*.md
```
Human-readable docs with usage notes, gotchas, tested examples, and verified schemas. Built from the official specs plus real-world testing.

### How to Use Both Layers

1. **Start with the enriched docs** in `<knowledge-base>/ghl/api/` for endpoints you've documented. These have context the raw specs don't.
2. **Fall back to the official specs** in `<knowledge-base>/ghl/highlevel-api-docs/apps/` for endpoints you haven't enriched yet. These JSON files contain complete request/response schemas.
3. **If neither has what you need**, check the official web docs at https://marketplace.gohighlevel.com/docs/

### Key Files

| File | Purpose |
|------|---------|
| `index.md` | Master index of all API endpoints with documentation status |
| `highlevel-api-docs/apps/*.json` | Official OpenAPI specs (36 API domains) |
| `highlevel-api-docs/toc.json` | Table of contents for the official docs |
| `highlevel-api-docs/docs/oauth/` | Official OAuth guides and scopes |

### Enriched Docs (Layer 2)

An example layout once you've started enriching docs. Endpoint counts will vary with the API.

| File | Endpoints |
|------|-----------|
| `api/contacts.md` | 27 |
| `api/opportunities.md` | 11 |
| `api/custom-fields.md` | 14 |
| `api/custom-values.md` | 5 |
| `api/calendars.md` | 34 |
| `api/locations.md` | 29 |
| `api/products.md` | 27 |
| `api/store.md` | 18 |
| `api/oauth.md` | 3 |
| `api/funnels.md` | 7 |
| `api/forms.md` | 3 |
| `api/surveys.md` | 2 |
| `api/workflows.md` | 1 |
| `api/campaigns.md` | 1 |
| `api/invoices.md` | 42 |
| `api/payments.md` | 24 |
| `api/users.md` | 7 |
| `api/conversations.md` | Partial |
| `api/voice-ai.md` | 4 |
| `api/social-media-posting.md` | In progress |

### Official Specs Without Enriched Docs Yet

These are available in `highlevel-api-docs/apps/` but may not have enriched docs in `api/` yet:

| Spec File | Domain |
|-----------|--------|
| `agencies.json` | Agencies |
| `associations.json` | Associations |
| `blogs.json` | Blogs |
| `businesses.json` | Businesses |
| `companies.json` | Companies |
| `courses.json` | Courses |
| `custom-menus.json` | Custom Menus |
| `email-isv.json` | Email ISV |
| `emails.json` | Emails |
| `links.json` | Links |
| `marketplace.json` | Marketplace |
| `medias.json` | Medias |
| `objects.json` | Objects |
| `phone-system.json` | Phone System |
| `proposals.json` | Proposals |
| `saas-api.json` | SaaS API |
| `snapshots.json` | Snapshots |

For these, read the JSON spec file directly. Each contains OpenAPI-format endpoint definitions with paths, methods, parameters, request bodies, and response schemas.

## API Quick Reference

### Base URL
```
https://services.leadconnectorhq.com
```

### Version Header
```
Version: 2021-07-28
```
Note: Conversations API uses `Version: 2021-04-15`

### Authentication
```
Authorization: Bearer {access_token}
```

### Rate Limits
- Burst: 100 requests / 10 seconds
- Daily: 200,000 requests / day

## Known gotchas

Hard-won findings from a read-only PIT audit of a live sub-account. Apply these before debugging mysterious failures.

### 1. Cloudflare blocks default scripting User-Agents

`services.leadconnectorhq.com` sits behind Cloudflare. Requests with `Python-urllib/x.x` or other bare UAs return `403 Cloudflare Error 1010` regardless of token validity. This is not a GHL-side block, and it is the most likely cause if a previously working script suddenly returns uniform 403s.

Fix: send a real-browser UA header on every request.

```python
headers = {
    "Authorization": f"Bearer {token}",
    "Version": "2021-07-28",
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36",
}
```

`curl` works without setting `-A` because it sends its own UA. n8n HTTP Request nodes, `requests`, `urllib`, and `httpx` all need the UA set explicitly. Playwright works because it sends a real-browser UA by default.

### 2. Email templates are not at the legacy endpoint

`GET /locations/{id}/templates` returns 0 templates even when many exist. Use the Email Builder endpoint and traverse folders:

```
GET /emails/builder?parentId={folderId}
```

Email Builder is the canonical template store now. The legacy `templates` endpoint is effectively dead for email reads.

### 3. GHL ships a typo: `monetory` (not `monetary`)

Currency fields use type `monetory` in form schemas and field definitions. Match on `monetory` when parsing field types. Do not try to "fix" it; the typo is in production data.

### 4. Agency-only endpoints that 401 with a location-scope PIT

These require an agency-level token. A location PIT cannot read them:

| Endpoint | Note |
|---|---|
| `GET /snapshots/` | Agency-only |
| `GET /companies/{id}` | Agency-only |
| `GET /custom-menus/` | Agency-only |
| `GET /forms/{id}` | Per-form detail. A location PIT can `GET /forms/` to list forms but not read the schema for any individual form. |
| `GET /phone-system/` (root) | Only `/phone-system/numbers` works at location scope. |

If you only have a location PIT and can't read a form's field schema via `GET /forms/{id}`, the schema is still recoverable read-only from the form's public widget URL:

```
https://api.leadconnectorhq.com/widget/form/{formId}
```

The widget page is built with Nuxt 3 and embeds the full form schema as a `__NUXT_DATA__` JSON payload in the HTML. You don't need a live browser — `curl` the widget URL with a real-browser UA (see gotcha #1), then extract and parse the `__NUXT_DATA__` blob from the returned HTML. Note that `__NUXT_DATA__` is a flattened/indexed array graph, so resolving it back into a normal object takes a small parser that follows the index references.

## Reading the Official JSON Specs

The files in `highlevel-api-docs/apps/` are OpenAPI 3.0 specs. Key structure:

```
{
  "openapi": "3.0.0",
  "paths": {
    "/endpoint/path": {
      "get": {
        "summary": "...",
        "parameters": [...],
        "responses": {...}
      }
    }
  },
  "components": {
    "schemas": {...}
  }
}
```

To extract endpoint info from a spec file, look at:
- `paths` for all available endpoints
- Each path's methods (get, post, put, delete) for operations
- `parameters` for query/path params
- `requestBody` for POST/PUT body schemas
- `responses` for response schemas
- `components.schemas` for shared model definitions

## Keeping the Official Specs Updated

```bash
cd <knowledge-base>/ghl/highlevel-api-docs
git pull
```

## Common Tasks

### Finding an Endpoint
1. Read the index: `<knowledge-base>/ghl/index.md`
2. If documented, read the enriched file from `<knowledge-base>/ghl/api/`
3. If not enriched, read the JSON spec from `<knowledge-base>/ghl/highlevel-api-docs/apps/`

### Getting Request/Response Schema
- Enriched docs: search for the endpoint anchor (e.g., `#create-contact`)
- JSON specs: parse the `paths` object for the endpoint path

### Checking Required Scopes
- Enriched docs list scopes per endpoint
- JSON specs include scope requirements in security definitions

## External Resources

- [GHL API Documentation](https://marketplace.gohighlevel.com/docs/)
- [GHL Developer Portal](https://marketplace.gohighlevel.com/)
- [OAuth Scopes Reference](https://marketplace.gohighlevel.com/docs/Authorization/Scopes/index.html)
- [GHL GitHub API Docs](https://github.com/GoHighLevel/highlevel-api-docs)

## Self-Improvement Mandate

**Every agent that uses this documentation should help keep it accurate.**

When you call a GHL API endpoint and discover that the docs are wrong (wrong field names, missing required fields, incorrect schemas), you should:

1. Fix the relevant doc file in `<knowledge-base>/ghl/api/`
2. Add a `> **Tested:** {date}` note to the endpoint you verified
3. Update the Known Issues Log in `<knowledge-base>/ghl/index.md`
4. Change endpoint status from unverified to verified if you confirmed it with a live API call
