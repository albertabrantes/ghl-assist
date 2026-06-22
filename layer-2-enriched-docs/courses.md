# Courses (Memberships) API

> Spec: `layer-1-official-docs/apps/courses.json`
> Base URL: `https://services.leadconnectorhq.com`
> Header: `Version: 2021-07-28`
> Auth: Bearer (PIT or OAuth Sub-Account access token)

## Endpoints

### POST `/courses/courses-exporter/public/import` — Import Courses

Bulk-create one or more course "products" with categories, sub-categories, and posts (lessons) inside a location's Memberships area.

**Request body** (`PublicExporterPayload`):

```json
{
  "locationId": "<sub-account location id>",
  "userId": "<optional user id>",
  "products": [
    {
      "title": "Course Title",
      "description": "Course description (required)",
      "imageUrl": "https://...",
      "instructorDetails": { "name": "...", "description": "..." },
      "categories": [
        {
          "title": "Module 1",
          "visibility": "published",
          "thumbnailUrl": "https://...",
          "posts": [
            {
              "title": "Lesson 1",
              "visibility": "published",
              "contentType": "video",
              "description": "Lesson description (required)",
              "thumbnailUrl": "https://...",
              "bucketVideoUrl": "https://<GHL-INTERNAL-S3-URL>",
              "postMaterials": [
                { "title": "Worksheet", "type": "pdf", "url": "https://..." }
              ]
            }
          ],
          "subCategories": []
        }
      ]
    }
  ]
}
```

**Response (201 Created):**

```json
{
  "message": "Migration for courses started",
  "note": "The copying of courses may take some time and will run in the background...",
  "processingCourses": [
    { "id": "<uuid>", "title": "...", "url": "https://app.<domain>/v2/location/<loc>/memberships/courses/products/<uuid>" }
  ]
}
```

## Critical gotchas

> **Tested**

### `visibility` is `published` / `draft`, NOT `public` / `private`

The schema enum is `["published", "draft"]`. Using `"public"` will fail validation. The `marketplace.gohighlevel.com` example docs occasionally show `"public"` — that is wrong.

### `bucketVideoUrl` does NOT accept external URLs

`bucketVideoUrl` only accepts URLs from GHL's own internal media bucket (i.e., a file already uploaded via `/medias/upload-file` or the Media Storage UI). External URLs (Gumlet/AWS-signed URLs, Cloudfront, Bunny, etc.) are **silently dropped**:

- The API returns `201 Created` with a happy "migration started" message.
- The course, module, and post (lesson) are all created cleanly with correct titles and "Published" visibility.
- The video field is left empty. The lesson editor shows "Select what media you would like to attach to this post" with no error message and no video.

If you need to import videos from outside GHL, you must:
1. Upload them to GHL Media Storage via `POST /medias/upload-file` (limit ~500 MB per file via API; 5 GB per video via UI), then
2. Reference the resulting GHL media URL as `bucketVideoUrl` in the import payload.

There is no documented way to make GHL pull a video server-side from an arbitrary external URL via the courses API.

### Required fields

- `PublicExporterPayload`: `locationId`, `products`
- `ProductInterface`: `title`, `description`, `categories`
- `CategoryInterface`: `title`, `visibility`
- `PostInterface`: `title`, `visibility`, `contentType`, `description`
- `PostMaterialInterface`: `title`, `type`, `url`

### `contentType` enum

`["video", "assignment", "quiz"]` — only `"video"` is well-tested via this endpoint. Assignment/quiz semantics for content payload are not documented.

### `postMaterials.type` enum

Long enum supporting most file types (pdf, mp4, jpg, png, mp3, etc.). See spec for full list.

### Async processing, no status endpoint

The 201 response says "migration started" but there is **no documented status/progress endpoint**. You can only verify by:
- GETting the course URL in the UI (requires browser auth), or
- Listing media files (`/medias/files`) for the location to see what was pulled — but this requires `medias.readonly` scope on the PIT.

### Auth scope

Requires the courses scope on the PIT. (Exact name not in public scope list — the spec just says "bearer".)

## Working test recipe

A minimal import script that works:
- Builds a minimal one-product/one-module/one-lesson payload.
- POSTs it with PIT auth and `Version: 2021-07-28`.
- Returns 201 and creates the course skeleton (video field will be empty unless you supply a GHL-internal URL).
