# Custom Fields API

**Base URL:** `https://services.leadconnectorhq.com`
**API Version Header:** `Version: 2021-07-28`

The Custom Fields API manages custom fields across GHL objects — Contacts, Opportunities, Custom Objects, and Business entities. There are two API versions: the **Legacy API** (Contacts/Opportunities) and the **V2 API** (Custom Objects/Business). This reference is split into the files below.

| File | Covers |
|------|--------|
| [field-types.md](./field-types.md) | Overview of Legacy vs V2, required scopes, authentication, rate limits, field key format, all `dataType` values (TEXT, NUMERICAL, MONETORY, PHONE, EMAIL, DATE, CHECKBOX, SINGLE_OPTIONS, MULTIPLE_OPTIONS, RADIO, SIGNATURE, FILE_UPLOAD), accepted file formats, and the options-format-per-type reference |
| [crud.md](./crud.md) | Field create/read/update/delete for both APIs — Legacy (Get, Get by ID, Create, Update, Delete, Upload File) and V2 (Get by Object Key, Get, Create, Update, Delete); setting/retrieving field values in records; webhook events |
| [folders.md](./folders.md) | Folder endpoints — Create Folder (Legacy, via `documentType: "folder"`), V2 Create/Update/Delete Folder, assigning fields to folders, and the Legacy → V2 migration guide |
| [gotchas.md](./gotchas.md) | Quirks and pitfalls — SINGLE_OPTIONS/CHECKBOX options-format errors, the `MONETORY` typo, field key immutability, Legacy PUT accepting `parentId`, folders missing from list responses, error codes, common validation errors, and best practices |
