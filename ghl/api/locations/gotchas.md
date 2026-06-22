# Locations API — Errors & Gotchas

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

## Error Responses

All endpoints return standardized error formats:

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 201 | Created (some POST endpoints) |
| 400 | Bad Request — invalid parameters or body |
| 401 | Unauthorized — invalid/expired token or insufficient scope |
| 422 | Unprocessable Entity — validation failure |
| 429 | Rate Limited — exceeded burst or daily limit |

---

## Gotchas

- **Agency Pro plan requirement:** The `POST /locations/` (Create Location) endpoint and the `locations.write` scope are only available on the **Agency Pro ($497/month)** plan. The $297 Agency Unlimited plan cannot create locations via API.
- **Delete Location scope discrepancy:** The OpenAPI spec lists `locations.internal-access-only` while community sources show `locations.write`. Verify with HighLevel developer support if your app needs delete capability.
- **Template delete uses readonly scope:** The delete template endpoint uses `locations/templates.readonly`, which is counterintuitive but confirmed. No `locations/templates.write` scope exists — template creation via API is not supported.
- **`MONETORY` spelling:** The custom field `dataType` value for currency fields is spelled `MONETORY`, not `MONETARY`. This is intentional and must be used exactly as spelled.
- **Custom Fields V2 is separate:** The location-level custom fields at `/locations/{locationId}/customFields` are the legacy endpoints. A newer Custom Fields V2 API exists at `/custom-fields/` with folder support. Both share the same OAuth scopes.
- **No "Snippets" endpoints** exist in the current API despite community references.
- **Version header is mandatory.** All Location endpoints use `Version: 2021-07-28`.
- **Search returns abbreviated objects:** `GET /locations/search` omits the `settings`, `social`, and `business` nested objects. Use Get Location for full details.
- **Twilio/Mailgun config is write-only:** `twilio` and `mailgun` configurations are accepted on create but are **not** returned in GET responses for security reasons.
