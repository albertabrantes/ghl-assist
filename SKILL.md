---
name: ghl-assist
description: GoHighLevel (GHL) API expert providing endpoint documentation, request/response schemas, authentication guidance, and implementation examples. This skill should be used when working with GHL/GoHighLevel API integrations, building workflows or automations that call GHL, writing code that interacts with GHL endpoints, or needing to understand GHL API capabilities, scopes, rate limits, or data structures.
---

# GHL API Knowledge Base

## Overview

This skill provides comprehensive knowledge about the GoHighLevel (GHL) API. Documentation is maintained in a two-layer system: the official GHL OpenAPI specs (cloned locally) plus your own enriched endpoint docs.

## Knowledge Base Location

All documentation lives **inside this skill directory** — no external paths to configure. The two layers sit side by side at the skill root:

```
<skill>/
  index.md                  Master index + "Which file for what" routing table + Known Gotchas
  layer-1-official-docs/     Official GHL OpenAPI specs (you clone these — see below)
  layer-2-enriched-docs/     Enriched, human-readable per-API docs (bundled with this skill)
```

### Two-Layer Documentation System

**Layer 1: Official OpenAPI Specs (source of truth)**
```
layer-1-official-docs/apps/*.json
```
Machine-readable OpenAPI/JSON specs for every GHL API domain. These are **not bundled** — clone them into the skill once:

```bash
cd <skill>
git clone https://github.com/GoHighLevel/highlevel-api-docs layer-1-official-docs
```
Keep them current with `git pull` inside `layer-1-official-docs/`.

**Layer 2: Enriched Agent Docs (bundled)**
```
layer-2-enriched-docs/*.md   (or */README.md for split domains)
```
Human-readable docs with usage notes, gotchas, examples, and request/response schemas — built from the official specs, with many endpoints verified against a live account and the rest marked 🔬 unverified in `index.md`. These ship with the skill.

### How to Use Both Layers

1. **Start with the enriched docs** in `layer-2-enriched-docs/` — they have context the raw specs don't. Use `index.md` to find the right file for a given GHL area, then open the per-API doc (or its folder `README.md`).
2. **Fall back to the official specs** in `layer-1-official-docs/apps/` for endpoints not yet enriched. These JSON files contain complete request/response schemas.
3. **If neither has what you need**, check the official web docs at https://marketplace.gohighlevel.com/docs/

Keep the enriched docs accurate as you work — see the Self-Improvement Mandate below.

### Key Files

| File | Purpose |
|------|---------|
| `index.md` | Master index of all API endpoints + routing table + Known Gotchas |
| `layer-2-enriched-docs/*.md` | Bundled enriched per-API docs |
| `layer-1-official-docs/apps/*.json` | Official OpenAPI specs (after you clone Layer 1) |
| `layer-1-official-docs/toc.json` | Table of contents for the official docs |
| `layer-1-official-docs/docs/oauth/` | Official OAuth guides and scopes |

### Official Specs Without Enriched Docs Yet

These are available in `layer-1-official-docs/apps/` but may not have enriched docs in `layer-2-enriched-docs/` yet:

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

`curl` works without setting `-A` because it sends its own UA. Most HTTP clients and workflow-tool HTTP nodes (e.g. `requests`, `urllib`, `httpx`) need the UA set explicitly. Playwright works because it sends a real-browser UA by default.

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

The files in `layer-1-official-docs/apps/` are OpenAPI 3.0 specs. Key structure:

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
cd <skill>/layer-1-official-docs
git pull
```

## Common Tasks

### Finding an Endpoint
1. Read the index: `index.md`
2. If documented, read the enriched file from `layer-2-enriched-docs/`
3. If not enriched, read the JSON spec from `layer-1-official-docs/apps/`

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

1. Fix the relevant doc file in `layer-2-enriched-docs/`
2. Add a `> **Tested:** {date}` note to the endpoint you verified
3. Update the Known Issues Log in `index.md`
4. Change endpoint status from unverified to verified if you confirmed it with a live API call
