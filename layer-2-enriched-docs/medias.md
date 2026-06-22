# Medias API

Base URL: `https://services.leadconnectorhq.com`

## Upload File

> **Tested:** Verified with a Private Integration Token.

**POST** `/medias/upload-file`

Upload a file to the GHL Media Library. Max file size: 25 MB.

**Scope:** `medias.write`

### Headers

| Header | Value |
|--------|-------|
| Authorization | Bearer {token} |
| Version | 2021-07-28 |
| Content-Type | multipart/form-data |

### Multipart Form Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| file | binary | Yes (if hosted=false) | The file to upload |
| hosted | boolean | No | If true, use fileUrl instead of file |
| fileUrl | string | Yes (if hosted=true) | URL of externally hosted file |
| name | string | No | Display name for the file |
| parentId | string | No | Folder ID to upload into |

### Important Notes

- Do NOT include `altId`, `altType`, or `locationId` in the upload request body. The token determines the location automatically.
- Set the correct `Content-Type` for the file part (e.g., `image/jpeg`, `image/png`). Using `application/octet-stream` may cause a 400 error.
- Do NOT set `hosted=true` unless you are providing a `fileUrl`. Sending `hosted=true` without a valid `fileUrl` causes a 400 error.

### Working Node.js Example

```javascript
async function uploadMedia(fileBuffer, fileName) {
  const boundary = `----FormBoundary${Date.now()}`;
  const crlf = '\r\n';

  // Detect content type from extension
  const ext = fileName.split('.').pop().toLowerCase();
  const mimeTypes = {
    jpg: 'image/jpeg', jpeg: 'image/jpeg',
    png: 'image/png', gif: 'image/gif',
    webp: 'image/webp', pdf: 'application/pdf'
  };
  const contentType = mimeTypes[ext] || 'application/octet-stream';

  // Build multipart body
  const filePart = Buffer.from(
    `--${boundary}${crlf}Content-Disposition: form-data; name="file"; filename="${fileName}"${crlf}Content-Type: ${contentType}${crlf}${crlf}`
  );
  const namePart = Buffer.from(
    `${crlf}--${boundary}${crlf}Content-Disposition: form-data; name="name"${crlf}${crlf}${fileName}`
  );
  const endPart = Buffer.from(`${crlf}--${boundary}--${crlf}`);

  const body = Buffer.concat([filePart, fileBuffer, namePart, endPart]);

  const res = await fetch('https://services.leadconnectorhq.com/medias/upload-file', {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${process.env.GHL_API_TOKEN}`,
      Version: '2021-07-28',
      'Content-Type': `multipart/form-data; boundary=${boundary}`,
    },
    body,
  });

  const data = await res.json();
  return { fileId: data.fileId, url: data.url };
}
```

### Working curl Example

```bash
curl -X POST https://services.leadconnectorhq.com/medias/upload-file \
  -H "Authorization: Bearer $GHL_API_TOKEN" \
  -H "Version: 2021-07-28" \
  -F "file=@/path/to/photo.png" \
  -F "name=photo.png"
```

### Response

```json
{
  "fileId": "EXAMPLEFileId0000000001",
  "url": "https://assets.cdn.filesafe.space/{locationId}/media/{uuid}.png",
  "traceId": "00000000-0000-0000-0000-000000000000"
}
```

---

## List Files/Folders

**GET** `/medias/files`

**Scope:** `medias.readonly`

### Required Query Parameters

| Param | Type | Description |
|-------|------|-------------|
| altType | string | Must be `location` |
| altId | string | Location ID |
| sortBy | string | Field to sort by (e.g., `createdAt`) |
| sortOrder | string | `asc` or `desc` |
| type | string | `file` or `folder` |

### Optional Query Parameters

| Param | Type | Description |
|-------|------|-------------|
| offset | string | Number of files to skip |
| limit | string | Number of files to return |
| query | string | Search text |
| parentId | string | Parent folder ID |
| fetchAll | string | `true` to fetch all |

---

## Delete File/Folder

**DELETE** `/medias/{id}`

**Scope:** `medias.write`

### Required Query Parameters

| Param | Type | Description |
|-------|------|-------------|
| altType | string | Must be `location` |
| altId | string | Location ID |

---

## Gotchas and Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| 401 | Token missing `medias.write` scope | Add Edit Medias scope to Private Integration Token |
| 400 with "hosted" | Sent `hosted=true` without valid `fileUrl` | Remove `hosted` field when uploading a file directly |
| 400 with content type | Wrong MIME type on file part | Set proper Content-Type (image/jpeg, image/png, etc.) |
