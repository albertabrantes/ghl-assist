# Custom Fields API — Gotchas, Errors & Best Practices

Quirks, validation errors, and notes for the GHL Custom Fields API. See [crud.md](./crud.md) for endpoints, [field-types.md](./field-types.md) for data types, and [folders.md](./folders.md) for folders.

---

## Options Format Gotchas

Different data types require different options formats. Using the wrong format returns validation errors.

> **SINGLE_OPTIONS Gotcha:**
> - `SINGLE_OPTIONS` does NOT accept `picklistOptions` — returns error: `"property picklistOptions should not exist"`
> - `SINGLE_OPTIONS` does NOT accept `options` with `{value, label}` objects — returns error: `"v.trim is not a function"`
> - `SINGLE_OPTIONS` ONLY accepts `options` as a plain string array: `["Option A", "Option B"]`
> - Despite using `options` in the request, the response returns the values under `picklistOptions`

> **CHECKBOX Gotcha:**
> - `CHECKBOX` does NOT accept `picklistOptions` — returns error: `"property picklistOptions should not exist"`
> - `CHECKBOX` does NOT accept `options` with `{value, label}` objects — returns error: `"v.trim is not a function"`
> - `CHECKBOX` ONLY accepts `options` as a plain string array: `["Option A", "Option B"]`
> - Despite using `options` in the request, the response returns the values under `picklistOptions`

For the full per-type options reference, see [field-types.md](./field-types.md).

---

## `MONETORY` Typo

The currency data type is spelled `MONETORY` (not `MONETARY`) in the GHL API. This is a known misspelling baked into the API — you must use `"dataType": "MONETORY"` exactly, and responses return `MONETORY` as well.

---

## Field Key Immutability

The `fieldKey` is set at creation time based on the original field name and never changes, even if you later rename the field. Renaming the display name does NOT change the underlying `fieldKey`. This keeps form mappings stable across renames.

You also cannot change `dataType` or `model`/`objectKey` after creation.

---

## Legacy PUT Accepts `parentId`

The Legacy PUT endpoint accepts `parentId`, so you can rename a field AND move it to a different folder in a single request. The field ID stays the same, so form mappings are preserved. This is undocumented but confirmed working. See the Update example in [crud.md](./crud.md).

---

## Folders Not in List Response

Folders are NOT returned in the `GET /locations/{locationId}/customFields` response. They exist only as `parentId` references on fields; the folder metadata itself is not included in list responses. To verify a folder exists, check if any fields reference its ID via `parentId`. See [folders.md](./folders.md).

---

## Error Responses

| Status Code | Description |
|-------------|-------------|
| `400` | Bad Request - Invalid parameters |
| `401` | Unauthorized - Invalid or missing token |
| `403` | Forbidden - Insufficient scope |
| `404` | Not Found - Resource doesn't exist |
| `422` | Unprocessable Entity - Validation error |
| `429` | Too Many Requests - Rate limit exceeded |

### Error Response Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Field name is required",
    "details": [
      {
        "field": "name",
        "message": "This field is required"
      }
    ]
  }
}
```

### Common Validation Errors

| Error | Description | Solution |
|-------|-------------|----------|
| `DUPLICATE_FIELD_KEY` | Field key already exists | Use a different field name |
| `INVALID_DATA_TYPE` | Unsupported data type | Check the Data Types reference |
| `INVALID_OPTIONS` | Missing or invalid options for selection type | Provide valid options array |
| `FIELD_IN_USE` | Cannot delete field with data | Remove data first or force delete |
| `INVALID_OBJECT_KEY` | Object key doesn't exist | Verify object key is valid |
| `FOLDER_NOT_FOUND` | Folder ID doesn't exist | Check folder ID or create folder first |

---

## Best Practices

### Naming Conventions

1. **Use descriptive names**: `Preferred Contact Method` is better than `Method`
2. **Be consistent**: Use either "Email" or "E-mail" throughout, not both
3. **Avoid special characters**: Stick to letters, numbers, spaces, and underscores
4. **Keep it concise**: Aim for 2-5 words

### Field Organization

1. **Use folders** (V2 API) to group related fields
2. **Set logical positions** to control display order
3. **Group required fields** at the top
4. **Separate sensitive information** into its own folder

### Performance

1. **Fetch fields once** and cache the configuration
2. **Use filters** when fetching fields to reduce payload size
3. **Batch updates** when modifying multiple fields
4. **Index frequently queried** custom fields

### Data Integrity

1. **Set validation rules** using appropriate data types
2. **Use dropdowns** instead of free text when possible
3. **Mark critical fields** as required
4. **Document field purpose** in field descriptions

---

## References

- [HighLevel API Documentation](https://marketplace.gohighlevel.com/docs/)
- [OAuth Scopes Reference](https://marketplace.gohighlevel.com/docs/Authorization/Scopes/index.html)
- [Custom Objects API](https://highlevel.stoplight.io/docs/integrations/custom-objects)
- [GitHub API Docs Repository](https://github.com/GoHighLevel/highlevel-api-docs)
