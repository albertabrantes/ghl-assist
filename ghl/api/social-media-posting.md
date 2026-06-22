# Social Media Posting API

Documentation for GoHighLevel Social Media Posting (Social Planner) endpoints.

**Base URL:** `https://services.leadconnectorhq.com`
**Version Header:** `Version: 2021-07-28`
**Required Scopes:** `socialplanner/account.readonly`, `socialplanner/post.readonly`, `socialplanner/post.write`, `socialplanner/category.readonly`, `socialplanner/oauth.readonly`, `socialplanner/csv.write`

> **Tested:** All endpoints below verified with live API calls using a PIT token.

---

## Table of Contents

- [Accounts](#accounts)
  - [Get Accounts](#get-accounts)
  - [Delete Account](#delete-account)
- [Posts](#posts)
  - [List Posts](#list-posts)
  - [Create Post](#create-post)
  - [Get Post](#get-post)
  - [Update Post](#update-post)
  - [Delete Post](#delete-post)
  - [Bulk Delete Posts](#bulk-delete-posts)
- [Categories](#categories)
  - [Get Categories](#get-categories)
  - [Get Category by ID](#get-category-by-id)
- [Tags](#tags)
  - [Get Tags](#get-tags)
  - [Get Tags by IDs](#get-tags-by-ids)
- [Statistics](#statistics)
  - [Get Social Media Statistics](#get-social-media-statistics)
- [OAuth](#oauth)
  - [Start Google OAuth](#start-google-oauth)
  - [Get Google Business Locations](#get-google-business-locations)
  - [Set Google Business Locations](#set-google-business-locations)
  - [Start Facebook OAuth](#start-facebook-oauth)
  - [Get Facebook Pages](#get-facebook-pages)
  - [Attach Facebook Pages](#attach-facebook-pages)
  - [Start Instagram OAuth](#start-instagram-oauth)
  - [Get Instagram Accounts](#get-instagram-accounts)
  - [Attach Instagram Accounts](#attach-instagram-accounts)
  - [Start LinkedIn OAuth](#start-linkedin-oauth)
  - [Get LinkedIn Pages/Profile](#get-linkedin-pagesprofile)
  - [Attach LinkedIn Pages/Profile](#attach-linkedin-pagesprofile)
  - [Start Twitter OAuth](#start-twitter-oauth)
  - [Get Twitter Profile](#get-twitter-profile)
  - [Attach Twitter Profile](#attach-twitter-profile)
  - [Start TikTok OAuth](#start-tiktok-oauth)
  - [Get TikTok Profile](#get-tiktok-profile)
  - [Attach TikTok Profile](#attach-tiktok-profile)
  - [Start TikTok Business OAuth](#start-tiktok-business-oauth)
  - [Get TikTok Business Profile](#get-tiktok-business-profile)
- [CSV Import](#csv-import)
  - [Upload CSV](#upload-csv)
  - [Get Upload Status](#get-upload-status)
  - [Set Accounts for CSV](#set-accounts-for-csv)
  - [Get CSV Post](#get-csv-post)
  - [Start CSV Finalize](#start-csv-finalize)
  - [Delete CSV](#delete-csv)
  - [Delete CSV Post](#delete-csv-post)
- [Known Issues and Gotchas](#known-issues-and-gotchas)

---

## Accounts

### Get Accounts

Retrieve all connected social media accounts for a location. This is the first endpoint you should call -- the returned account IDs are needed for creating posts.

**Endpoint:** `GET /social-media-posting/{locationId}/accounts`

**Required Scope:** `socialplanner/account.readonly`

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |

#### Headers

```
Authorization: Bearer {access_token}
Version: 2021-07-28
Accept: application/json
```

#### Example Request

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/accounts" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Accept: application/json"
```

#### Response (200 OK)

> **Tested:** Confirmed working. Returns connected Facebook and Instagram accounts.

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Fetched Accounts",
  "results": {
    "accounts": [
      {
        "id": "69b0000000000000aaaa0003_EXAMPLELocation00000_520000000000001_page",
        "oauthId": "69b0000000000000aaaa0003",
        "profileId": "69b0000000000000aaaa0005",
        "name": "Tech News",
        "avatar": "https://storage.googleapis.com/highlevel-backend.appspot.com/social-media-posting/profile-picture/facebook/pages/520000000000001.jpg",
        "platform": "facebook",
        "type": "page",
        "expire": "2026-05-09T15:20:45.176Z",
        "isExpired": false,
        "originId": "520000000000001",
        "deleted": false,
        "updatedAt": "2026-03-10T15:25:58.230Z",
        "hasStatisticsPermissions": true,
        "buildingStatistics": false,
        "syncPosts": false
      },
      {
        "id": "69b0000000000000aaaa0001_EXAMPLELocation00000_10000000000000001",
        "oauthId": "69b0000000000000aaaa0001",
        "profileId": "69b0000000000000aaaa0002",
        "name": "myaccount",
        "platform": "instagram",
        "type": "profile",
        "expire": "2026-05-09T15:21:21.466Z",
        "isExpired": false,
        "originId": "10000000000000001",
        "meta": {
          "pageId": "520000000000001",
          "loginType": "facebook"
        },
        "deleted": false,
        "updatedAt": "2026-03-10T15:26:27.250Z",
        "hasStatisticsPermissions": true,
        "buildingStatistics": false,
        "syncPosts": false
      }
    ],
    "groups": []
  }
}
```

#### Account Object Schema

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | **The account ID to use in `accountIds` when creating posts.** Format: `{oauthId}_{locationId}_{originId}[_{type}]` |
| `oauthId` | string | Internal OAuth connection ID |
| `profileId` | string | Profile ID (used for statistics endpoint) |
| `name` | string | Display name of the account/page |
| `avatar` | string | URL to profile picture |
| `platform` | string | Platform: `google`, `facebook`, `instagram`, `linkedin`, `twitter`, `tiktok` |
| `type` | string | Account type: `page`, `profile`, `location`, `group` |
| `expire` | string | OAuth token expiration date (ISO 8601) |
| `isExpired` | boolean | Whether the OAuth token has expired |
| `originId` | string | Platform-specific ID (e.g., Facebook page ID) |
| `meta` | object | Platform-specific metadata |
| `deleted` | boolean | Whether account is soft-deleted |
| `hasStatisticsPermissions` | boolean | Whether analytics data can be fetched |
| `buildingStatistics` | boolean | Whether statistics are currently being built |
| `syncPosts` | boolean | Whether posts are being synced |

---

### Delete Account

Remove a connected social media account.

**Endpoint:** `DELETE /social-media-posting/{locationId}/accounts/{id}`

**Required Scope:** Not specified (no explicit scope in OpenAPI spec)

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `id` | string | Yes | Account ID to delete |

#### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `companyId` | string | No | Company/agency ID |
| `userId` | string | No | User ID |

#### Response (200 OK)

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Deleted Account",
  "results": {
    "locationId": "ve9EPM428h8vShlRW1KT",
    "id": "EXAMPLEAccountId00000001"
  }
}
```

---

## Posts

### List Posts

Retrieve a paginated, filtered list of social media posts.

**Endpoint:** `POST /social-media-posting/{locationId}/posts/list`

**Required Scope:** `socialplanner/post.readonly`

> **NOTE:** This is a POST endpoint, not GET. The filters are sent in the request body.

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |

#### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | string | No | Filter type: `recent`, `all`, `scheduled`, `draft`, `failed`, `in_review`, `published`, `in_progress`, `deleted`. Default: `all` |
| `accounts` | string | No | Comma-separated list of account IDs to filter by |
| `skip` | string | **Yes** | Pagination offset. Default: `"0"` |
| `limit` | string | **Yes** | Results per page. Default: `"10"` |
| `fromDate` | string | **Yes** | Start date filter (ISO 8601). Example: `"2024-01-22T05:32:49.463Z"` |
| `toDate` | string | **Yes** | End date filter (ISO 8601). Example: `"2024-03-20T05:32:49.463Z"` |
| `includeUsers` | string | **Yes** | Include user data: `"true"` or `"false"` |
| `postType` | string | No | Post type filter: `post`, `story`, `reel` |

> **IMPORTANT:** `skip`, `limit`, `fromDate`, `toDate`, and `includeUsers` are all **required** per the OpenAPI spec. The values for `skip`, `limit`, and `includeUsers` are **strings**, not numbers/booleans.

#### Example Request

```bash
curl -X POST \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/posts/list" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "type": "all",
    "skip": "0",
    "limit": "10",
    "fromDate": "2024-01-01T00:00:00.000Z",
    "toDate": "2026-12-31T23:59:59.000Z",
    "includeUsers": "true"
  }'
```

#### Response (201 Created)

> **Tested:** Confirmed working. Returns 201 (not 200).

```json
{
  "success": true,
  "statusCode": 201,
  "message": "Fetched Posts",
  "results": {
    "posts": [],
    "count": 0
  }
}
```

#### Post Object Schema (in `posts` array)

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Post ID (MongoDB ObjectId) |
| `source` | string | Source: `composer`, `recurring`, `csv`, `review`, `rss` |
| `locationId` | string | Location ID |
| `platform` | string | Platform: `google`, `facebook`, `instagram`, `linkedin`, `twitter`, `tiktok` |
| `displayDate` | string | Display date (ISO 8601) |
| `createdAt` | string | Creation date (ISO 8601) |
| `updatedAt` | string | Last update date (ISO 8601) |
| `accountId` | string | Single account ID (for published posts) |
| `accountIds` | string[] | Array of target account IDs |
| `summary` | string | Post content/caption text |
| `media` | object[] | Media attachments array |
| `status` | string | Post status (see below) |
| `type` | string | Post type: `post`, `story`, `reel` |
| `tags` | string[] | Tag IDs |
| `error` | string | Error message (for failed posts) |
| `postId` | string | Platform-specific post ID (after publishing) |
| `publishedAt` | string | Publish date (ISO 8601) |
| `parentPostId` | string | Parent post UUID (groups multi-account posts) |
| `channel` | string | Channel: `public` |
| `deleted` | boolean | Whether post is soft-deleted |
| `user` | object | User who created the post (when `includeUsers: "true"`) |
| `postApprovalDetails` | object | Approval workflow details |
| `gmbPostDetails` | object | Google My Business specific details |
| `tiktokPostDetails` | object | TikTok specific details |
| `instagramPostDetails` | object | Instagram specific details |
| `ogTagsDetails` | object | Open Graph tag details |

#### Post Status Values

| Status | Description |
|--------|-------------|
| `draft` | Draft, not scheduled |
| `scheduled` | Scheduled for future publication |
| `in_progress` | Currently being published |
| `published` | Successfully published |
| `failed` | Publication failed |
| `in_review` | Awaiting approval |
| `notification_sent` | Notification sent to approver |
| `deleted` | Soft-deleted |

---

### Create Post

Create a new social media post (draft, scheduled, or for immediate publishing).

**Endpoint:** `POST /social-media-posting/{locationId}/posts`

**Required Scope:** `socialplanner/post.write`

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |

#### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `accountIds` | string[] | **Yes** | Array of connected account IDs from the accounts endpoint. **Required even for drafts.** |
| `userId` | string | **Yes** | User ID (location ID or actual user ID). **Required even for drafts -- the API will return 422 without it.** |
| `summary` | string | No | Post content/caption text |
| `media` | object[] | No | Media attachments (see Media Schema below) |
| `status` | string | No | Status: `draft`, `scheduled`, `in_review`. Default behavior depends on `scheduleDate` |
| `type` | string | No | Post type: `post`, `story`, `reel`. Default: `post` |
| `scheduleDate` | string | No | ISO 8601 date for scheduled posts |
| `createdBy` | string | No | User ID of the creator |
| `followUpComment` | string | No | Follow-up comment after publishing. Not allowed on TikTok and GMB. 280 char limit on Twitter. |
| `tags` | string[] | No | Array of tag IDs |
| `categoryId` | string | No | Category ID |
| `ogTagsDetails` | object | No | Open Graph metadata: `{ metaImage, metaLink }` |
| `postApprovalDetails` | object | No | Approval workflow details (see below) |
| `facebookPostDetails` | object | No | Facebook-specific settings |
| `instagramPostDetails` | object | No | Instagram-specific settings |
| `linkedinPostDetails` | object | No | LinkedIn-specific settings |
| `tiktokPostDetails` | object | No | TikTok-specific settings (see below) |
| `gmbPostDetails` | object | No | Google My Business settings (see below) |

#### Media Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `url` | string | **Yes** | Public URL to the media file |
| `type` | string | No | MIME type: `image/png`, `image/jpeg`, `video/mp4`, etc. |
| `caption` | string | No | Alt text/caption for the media |
| `thumbnail` | string | No | Thumbnail URL (for videos) |
| `defaultThumb` | string | No | Default thumbnail URL |
| `id` | string | No | Media ID |

#### TikTok Post Details Schema

| Field | Type | Description |
|-------|------|-------------|
| `privacyLevel` | string | `PUBLIC_TO_EVERYONE`, `MUTUAL_FOLLOW_FRIENDS`, `SELF_ONLY` |
| `promoteOtherBrand` | boolean | Promote other brand disclosure |
| `enableComment` | boolean | Enable comments |
| `enableDuet` | boolean | Enable duets |
| `enableStitch` | boolean | Enable stitches |
| `videoDisclosure` | boolean | Video disclosure |
| `promoteYourBrand` | boolean | Promote your brand disclosure |

#### GMB Post Details Schema

| Field | Type | Description |
|-------|------|-------------|
| `gmbEventType` | string | `STANDARD`, `EVENT`, `OFFER` |
| `title` | string | Event title |
| `offerTitle` | string | Offer title |
| `startDate` | object | `{ startDate: { year, month, day }, startTime: { hours, minutes, seconds } }` |
| `endDate` | object | `{ endDate: { year, month, day }, endTime: { hours, minutes, seconds } }` |
| `termsConditions` | string | Terms and conditions URL |
| `url` | string | CTA URL |
| `couponCode` | string | Coupon code |
| `redeemOnlineUrl` | string | Redeem online URL |
| `actionType` | string | `none`, `order`, `book`, `shop`, `learn_more`, `call`, `sign_up` |

#### Post Approval Schema

| Field | Type | Description |
|-------|------|-------------|
| `approver` | string | Approver user ID |
| `requesterNote` | string | Note from requester |
| `approverNote` | string | Note from approver |
| `approvalStatus` | string | `pending`, `approved`, `rejected`, `not_required` |

#### Example Request (Draft Post)

```bash
curl -X POST \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/posts" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "accountIds": ["69b0000000000000aaaa0003_EXAMPLELocation00000_520000000000001_page"],
    "userId": "EXAMPLELocation00000",
    "summary": "Check out our latest updates!",
    "status": "draft",
    "type": "post"
  }'
```

#### Example Request (Scheduled Post with Image)

```bash
curl -X POST \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/posts" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "accountIds": ["69b0000000000000aaaa0003_EXAMPLELocation00000_520000000000001_page"],
    "userId": "EXAMPLELocation00000",
    "summary": "Exciting news coming soon! #socialmedia",
    "status": "scheduled",
    "type": "post",
    "scheduleDate": "2026-03-15T14:00:00.000Z",
    "media": [
      {
        "url": "https://example.com/image.jpg",
        "type": "image/jpeg"
      }
    ],
    "followUpComment": "What do you think? Let us know in the comments!"
  }'
```

#### Response (201 Created)

> **Tested:** Confirmed working. Draft post created successfully.

```json
{
  "success": true,
  "statusCode": 201,
  "message": "Created Post",
  "results": {
    "post": {
      "deleted": false,
      "source": "composer",
      "isCommentSyncing": false,
      "_id": "69b0000000000000aaaa0004",
      "locationId": "EXAMPLELocation00000",
      "platform": "google",
      "displayDate": "2026-03-10T15:29:18.286Z",
      "channel": "public",
      "parentPostId": "5f04e153-b20a-4fc7-a39b-b6dc76a6195a",
      "createdAt": "2026-03-10T15:29:18.287Z",
      "updatedAt": "2026-03-10T15:29:18.287Z",
      "accountIds": ["69b0000000000000aaaa0003_EXAMPLELocation00000_520000000000001_page"],
      "summary": "API test draft post - safe to delete",
      "media": [],
      "status": "draft",
      "createdBy": "EXAMPLELocation00000",
      "type": "post",
      "tags": [],
      "ogTagsDetails": {},
      "user": null,
      "postApprovalDetails": {
        "approverUser": null,
        "approvalStatus": "not_required"
      },
      "gmbPostDetails": {},
      "tiktokPostDetails": {},
      "instagramPostDetails": {}
    }
  }
}
```

---

### Get Post

Retrieve a specific post by ID.

**Endpoint:** `GET /social-media-posting/{locationId}/posts/{id}`

**Required Scope:** Not explicitly specified (works with `socialplanner/post.readonly`)

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `id` | string | Yes | Post ID (`_id` from create/list response) |

#### Example Request

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/posts/69b0000000000000aaaa0004" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Accept: application/json"
```

#### Response (200 OK)

> **Tested:** Confirmed working. Returns the full post object.

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Fetched Post",
  "results": {
    "post": { /* Same schema as Create Post response */ }
  }
}
```

---

### Update Post

Update an existing post.

**Endpoint:** `PUT /social-media-posting/{locationId}/posts/{id}`

**Required Scope:** Not explicitly specified (works with `socialplanner/post.write`)

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `id` | string | Yes | Post ID to update |

#### Request Body

Same fields as Create Post. Key fields:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `accountIds` | string[] | No | Account IDs (can be updated) |
| `userId` | string | **Yes** | User ID -- **required or returns 422** |
| `summary` | string | No | Updated post content |
| `status` | string | No | Updated status |
| `type` | string | No | Updated post type |
| `scheduleDate` | string | No | Updated schedule date |
| `scheduleTimeUpdated` | boolean | No | Whether schedule time was changed |

#### Example Request

```bash
curl -X PUT \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/posts/69b0000000000000aaaa0004" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "accountIds": ["69b0000000000000aaaa0003_EXAMPLELocation00000_520000000000001_page"],
    "userId": "EXAMPLELocation00000",
    "summary": "Updated content here",
    "status": "draft",
    "type": "post"
  }'
```

#### Response (200 OK)

> **Tested:** Confirmed working. Note: `userId` is required or you get 422.

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Updated Post"
}
```

> **Note:** The update response does NOT return the updated post object. You must call GET to retrieve the updated post.

---

### Delete Post

Delete a specific post.

**Endpoint:** `DELETE /social-media-posting/{locationId}/posts/{id}`

**Required Scope:** Not explicitly specified (works with `socialplanner/post.write`)

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `id` | string | Yes | Post ID to delete |

#### Example Request

```bash
curl -X DELETE \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/posts/69b0000000000000aaaa0004" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Accept: application/json"
```

#### Response (200 OK)

> **Tested:** Confirmed working.

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Deleted Post",
  "results": {
    "postId": "69b0000000000000aaaa0004"
  }
}
```

---

### Bulk Delete Posts

Delete multiple posts at once. Maximum 50 posts per request.

**Endpoint:** `POST /social-media-posting/{locationId}/posts/bulk-delete`

**Required Scope:** Not explicitly specified

#### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `postIds` | string[] | **Yes** | Array of post IDs to delete (max 50) |

#### Example Request

```bash
curl -X POST \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/posts/bulk-delete" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "postIds": ["post_id_1", "post_id_2"]
  }'
```

#### Response (201)

```json
{
  "message": "Posts deleted successfully"
}
```

#### Error Responses

- **400:** Cannot delete more than 50 posts at a time
- **404:** No posts found with the given IDs
- **422:** `postIds` should not be empty

> **Tested:** Confirmed 422 validation for empty `postIds` array.

---

## Categories

### Get Categories

Retrieve social media post categories for a location.

**Endpoint:** `GET /social-media-posting/{locationId}/categories`

**Required Scope:** `socialplanner/category.readonly`

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |

#### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `searchText` | string | No | Search filter text |
| `limit` | number | No | Results per page |
| `skip` | number | No | Pagination offset |

#### Example Request

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/categories" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Accept: application/json"
```

#### Response (200 OK)

> **Tested:** Confirmed working. Returns empty array when no categories exist.

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Fetched Categories by Location ID",
  "results": {
    "categories": [],
    "count": 0
  }
}
```

---

### Get Category by ID

Retrieve a specific category.

**Endpoint:** `GET /social-media-posting/{locationId}/categories/{id}`

**Required Scope:** Not explicitly specified

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |
| `id` | string | Yes | Category ID |

#### Response (200 OK)

> **Tested:** Returns 200 with `"category": "Category not found"` for nonexistent IDs (does not return 404).

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Fetched Catagory",
  "results": {
    "category": { /* category object or "Category not found" string */ }
  }
}
```

> **Note:** The response message has a typo: "Catagory" (not "Category").

---

## Tags

### Get Tags

Retrieve social media post tags for a location.

**Endpoint:** `GET /social-media-posting/{locationId}/tags`

**Required Scope:** Not explicitly specified

#### Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |

#### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `searchText` | string | No | Search filter text |
| `limit` | number | No | Results per page |
| `skip` | number | No | Pagination offset |

#### Example Request

```bash
curl -X GET \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/tags" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Accept: application/json"
```

#### Response (200 OK)

> **Tested:** Confirmed working. Returns string message when no tags exist (not an empty array).

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Fetched Tags by Location ID",
  "results": {
    "tags": "Tags not found for Location Id",
    "count": 0
  }
}
```

> **GOTCHA:** When no tags exist, `tags` is a **string** ("Tags not found for Location Id"), NOT an empty array. Your code must handle both types.

---

### Get Tags by IDs

Retrieve specific tags by their IDs.

**Endpoint:** `POST /social-media-posting/{locationId}/tags/details`

**Required Scope:** Not explicitly specified

#### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `tagIds` | string[] | Yes | Array of tag IDs |

#### Example Request

```bash
curl -X POST \
  "https://services.leadconnectorhq.com/social-media-posting/EXAMPLELocation00000/tags/details" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"tagIds": ["tag_id_1", "tag_id_2"]}'
```

#### Response (201 Created)

> **Tested:** Confirmed working. Returns 201 (not 200).

```json
{
  "success": true,
  "statusCode": 201,
  "message": "Fetched Tags by Tag IDs",
  "results": {
    "tags": "Tags not found",
    "count": 0
  }
}
```

---

## Statistics

### Get Social Media Statistics

Retrieve analytics and statistics for connected social media accounts.

**Endpoint:** `POST /social-media-posting/statistics`

**Required Scope:** Not explicitly specified

#### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `locationId` | string | Yes | Location/sub-account ID |

#### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `profileIds` | string[] | Yes | Array of `profileId` values from the accounts endpoint (min 1, max 100) |
| `platforms` | string[] | No | Filter by platforms: `facebook`, `instagram`, `google`, `linkedin`, `twitter`, `tiktok` |

#### Example Request

```bash
curl -X POST \
  "https://services.leadconnectorhq.com/social-media-posting/statistics?locationId=EXAMPLELocation00000" \
  -H "Authorization: Bearer ${GHL_API_KEY}" \
  -H "Version: 2021-07-28" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "profileIds": ["69b0000000000000aaaa0005"],
    "platforms": ["facebook"]
  }'
```

#### Response (201 Created)

> **Tested:** Confirmed working. Returns comprehensive analytics data.

```json
{
  "results": {
    "dayRange": ["Tue", "Wed", "Thu", "Fri", "Sat", "Sun", "Mon"],
    "totals": {
      "posts": 0,
      "likes": 0,
      "followers": 0,
      "impressions": 0,
      "comments": 0
    },
    "postPerformance": {
      "posts": { "facebook": [0, 0, 0, 0, 0, 0, 0] },
      "impressions": [0, 0, 0, 0, 0, 0, 0],
      "likes": [0, 0, 0, 0, 0, 0, 0],
      "comments": [0, 0, 0, 0, 0, 0, 0]
    },
    "breakdowns": {
      "posts": {
        "total": 0,
        "totalChange": 0,
        "platforms": {
          "facebook": { "value": 0, "change": 0 }
        }
      },
      "impressions": { /* same structure */ },
      "reach": { /* same structure */ },
      "engagement": {
        "facebook": { "likes": 0, "comments": 0, "shares": 0, "change": 0 }
      }
    },
    "platformTotals": {
      "impressions": {
        "facebook": { "total": 0, "series": [0, 0, 0, 0, 0, 0, 0] }
      },
      "followers": { /* same structure */ },
      "likes": { /* same structure */ }
    },
    "demographics": {
      "gender": {
        "totals": {
          "male": { "total": 0, "percentage": 0 },
          "female": { "total": 0, "percentage": 0 },
          "unknown": { "total": 0, "percentage": 0 }
        }
      },
      "age": {
        "totals": {
          "13-17": 0, "18-24": 0, "25-34": 0,
          "35-44": 0, "45-54": 0, "55-64": 0, "65+": 0
        }
      }
    }
  },
  "message": "Analytics Built Successfully"
}
```

> **Note:** The `profileIds` come from the `profileId` field in the accounts response, NOT the `id` field.

---

## OAuth

OAuth endpoints are used to connect social media accounts. These initiate browser-based OAuth flows and are typically used from the GHL dashboard, not from API integrations. Documented here for completeness.

### Start Google OAuth

**Endpoint:** `GET /social-media-posting/oauth/google/start`

**Required Scope:** `socialplanner/oauth.readonly`

**Query Parameters:** `locationId` (required), `userId` (required), `page` (optional), `reconnect` (optional)

**Response:** 302 redirect to Google OAuth consent page.

### Get Google Business Locations

**Endpoint:** `GET /social-media-posting/oauth/{locationId}/google/locations/{accountId}`

### Set Google Business Locations

**Endpoint:** `POST /social-media-posting/oauth/{locationId}/google/locations/{accountId}`

**Request Body:** `AttachGMBLocationDTO` - `{ location: object, account: object, companyId: string }`

### Start Facebook OAuth

**Endpoint:** `GET /social-media-posting/oauth/facebook/start`

**Query Parameters:** `locationId` (required), `userId` (required), `page` (optional), `reconnect` (optional)

**Response:** 302 redirect to Facebook OAuth consent page.

> **Tested:** Returns 302 redirect as expected.

### Get Facebook Pages

**Endpoint:** `GET /social-media-posting/oauth/{locationId}/facebook/accounts/{accountId}`

Returns available Facebook pages/groups after OAuth.

### Attach Facebook Pages

**Endpoint:** `POST /social-media-posting/oauth/{locationId}/facebook/accounts/{accountId}`

**Request Body:** `{ type: "page", originId: string, name: string, avatar: string, companyId: string }`

### Start Instagram OAuth

**Endpoint:** `GET /social-media-posting/oauth/instagram/start`

**Required Scope:** `socialplanner/oauth.readonly`

**Query Parameters:** `locationId` (required), `userId` (required), `page` (optional), `reconnect` (optional)

### Get Instagram Accounts

**Endpoint:** `GET /social-media-posting/oauth/{locationId}/instagram/accounts/{accountId}`

### Attach Instagram Accounts

**Endpoint:** `POST /social-media-posting/oauth/{locationId}/instagram/accounts/{accountId}`

### Start LinkedIn OAuth

**Endpoint:** `GET /social-media-posting/oauth/linkedin/start`

**Query Parameters:** `locationId` (required), `userId` (required), `page` (optional), `reconnect` (optional)

### Get LinkedIn Pages/Profile

**Endpoint:** `GET /social-media-posting/oauth/{locationId}/linkedin/accounts/{accountId}`

### Attach LinkedIn Pages/Profile

**Endpoint:** `POST /social-media-posting/oauth/{locationId}/linkedin/accounts/{accountId}`

### Start Twitter OAuth

**Endpoint:** `GET /social-media-posting/oauth/twitter/start`

**Required Scope:** `socialplanner/oauth.readonly`

**Query Parameters:** `locationId` (required), `userId` (required), `page` (optional), `reconnect` (optional)

### Get Twitter Profile

**Endpoint:** `GET /social-media-posting/oauth/{locationId}/twitter/accounts/{accountId}`

### Attach Twitter Profile

**Endpoint:** `POST /social-media-posting/oauth/{locationId}/twitter/accounts/{accountId}`

### Start TikTok OAuth

**Endpoint:** `GET /social-media-posting/oauth/tiktok/start`

**Required Scope:** `socialplanner/oauth.readonly`

**Query Parameters:** `locationId` (required), `userId` (required), `page` (optional), `reconnect` (optional)

### Get TikTok Profile

**Endpoint:** `GET /social-media-posting/oauth/{locationId}/tiktok/accounts/{accountId}`

### Attach TikTok Profile

**Endpoint:** `POST /social-media-posting/oauth/{locationId}/tiktok/accounts/{accountId}`

### Start TikTok Business OAuth

**Endpoint:** `GET /social-media-posting/oauth/tiktok-business/start`

**Required Scope:** `socialplanner/oauth.readonly`

**Query Parameters:** `locationId` (required), `userId` (required), `page` (optional), `reconnect` (optional)

### Get TikTok Business Profile

**Endpoint:** `GET /social-media-posting/oauth/{locationId}/tiktok-business/accounts/{accountId}`

---

## CSV Import

Endpoints for bulk importing posts via CSV files.

### Upload CSV

**Endpoint:** `POST /social-media-posting/{locationId}/csv`

**Required Scope:** `socialplanner/csv.write`

### Get Upload Status

**Endpoint:** `GET /social-media-posting/{locationId}/csv`

**Query Parameters:** `skip`, `limit`, `includeUsers`, `userId` (required)

> **Tested:** `userId` is **required** as a query parameter or returns 422. Not documented in OpenAPI spec as required.

```json
{
  "success": true,
  "statusCode": 200,
  "message": "Fetched CSV Upload Status",
  "results": {
    "csvs": {},
    "count": 0
  }
}
```

### Set Accounts for CSV

**Endpoint:** `POST /social-media-posting/{locationId}/set-accounts`

**Required Scope:** `socialplanner/csv.write`

### Get CSV Post

**Endpoint:** `GET /social-media-posting/{locationId}/csv/{id}`

**Query Parameters:** `skip`, `limit`

### Start CSV Finalize

**Endpoint:** `PATCH /social-media-posting/{locationId}/csv/{id}`

### Delete CSV

**Endpoint:** `DELETE /social-media-posting/{locationId}/csv/{id}`

### Delete CSV Post

**Endpoint:** `DELETE /social-media-posting/{locationId}/csv/{csvId}/post/{postId}`

---

## Known Issues and Gotchas

### 1. `accountIds` and `userId` are ALWAYS Required for Creating/Updating Posts

Even for **draft** posts, the API requires both `accountIds` (array of connected account IDs) and `userId`. Without them you get a 422 error:

```json
{
  "statusCode": 422,
  "message": [
    "accountIds must be an array with Account IDs",
    "accountIds should not be empty",
    "userId must be a string",
    "userId should not be empty"
  ]
}
```

The official docs suggest `accountIds` is only required for non-draft posts -- this is **incorrect**.

### 2. Account IDs Must Come from the Accounts Endpoint

The `accountIds` format is complex: `{oauthId}_{locationId}_{originId}[_{type}]`. You cannot construct these yourself. Always fetch them from `GET /social-media-posting/{locationId}/accounts`.

### 3. Accounts Must Be Connected via OAuth in GHL Dashboard

There is no way to connect social media accounts purely via API. The OAuth endpoints (`/oauth/facebook/start`, etc.) redirect to browser-based flows. Accounts must be connected through the GHL UI first.

### 4. Wrong Endpoint Paths Return 401

The path `/social-media-posting/oauth/facebook/accounts?locationId=X` does **NOT** exist. The correct pattern is:
- `/social-media-posting/oauth/{locationId}/facebook/accounts/{accountId}`

The old/wrong paths return `401 Unauthorized` with "The token is not authorized for this scope."

> **Tested:** Confirmed 401 on wrong path.

### 5. Tags Response Type is Inconsistent

When tags exist, `results.tags` is an **array**. When no tags exist, it is a **string** ("Tags not found for Location Id"). Always check the type before iterating.

### 6. Category Endpoint Has a Typo

The response message says "Fetched Catagory" (misspelled). This does not affect functionality.

### 7. List Posts Returns 201, Not 200

The `POST /posts/list` endpoint returns HTTP 201 (Created), not 200 (OK), even though it is a read operation. This is unusual but confirmed.

### 8. Update Post Does NOT Return the Updated Object

Unlike Create Post and Get Post, the Update Post endpoint only returns a success message -- not the post object. You must make a separate GET call to fetch the updated post.

### 9. CSV Upload Status Requires `userId` Query Parameter

The OpenAPI spec lists `userId` as optional, but in practice it returns 422 without it. Always include `userId` as a query parameter.

### 10. Platforms Supported

The following social media platforms are supported:
- **Google** (Google My Business / Google Business Profile)
- **Facebook** (Pages)
- **Instagram** (Professional accounts, connected via Facebook)
- **LinkedIn** (Pages and profiles)
- **Twitter/X** (Profiles)
- **TikTok** (Personal and Business accounts)

### 11. Rate Limits

No specific rate limit documentation in the OpenAPI spec. Follow standard GHL API rate limits.

### 12. Required Scopes Summary

| Scope | Used By |
|-------|---------|
| `socialplanner/account.readonly` | Get Accounts |
| `socialplanner/post.readonly` | List Posts |
| `socialplanner/post.write` | Create/Update/Delete Posts |
| `socialplanner/category.readonly` | Get Categories |
| `socialplanner/oauth.readonly` | OAuth start endpoints (Google, Instagram, Twitter, TikTok) |
| `socialplanner/csv.write` | CSV upload and Set Accounts |

> **Note:** Many endpoints don't have explicit scope requirements in the OpenAPI spec. In practice, having the `socialplanner/*` scopes enabled on your PIT/OAuth token should cover all endpoints.

---

## Complete Endpoint Reference

| # | Endpoint | Method | Path | Scope | Status |
|---|----------|--------|------|-------|--------|
| | **Accounts** | | | | |
| 1 | Get Accounts | GET | `/social-media-posting/{locationId}/accounts` | `socialplanner/account.readonly` | ✅ |
| 2 | Delete Account | DELETE | `/social-media-posting/{locationId}/accounts/{id}` | -- | ✅ |
| | **Posts** | | | | |
| 3 | List Posts | POST | `/social-media-posting/{locationId}/posts/list` | `socialplanner/post.readonly` | ✅ |
| 4 | Create Post | POST | `/social-media-posting/{locationId}/posts` | `socialplanner/post.write` | ✅ |
| 5 | Get Post | GET | `/social-media-posting/{locationId}/posts/{id}` | -- | ✅ |
| 6 | Update Post | PUT | `/social-media-posting/{locationId}/posts/{id}` | -- | ✅ |
| 7 | Delete Post | DELETE | `/social-media-posting/{locationId}/posts/{id}` | -- | ✅ |
| 8 | Bulk Delete Posts | POST | `/social-media-posting/{locationId}/posts/bulk-delete` | -- | ✅ |
| | **Categories** | | | | |
| 9 | Get Categories | GET | `/social-media-posting/{locationId}/categories` | `socialplanner/category.readonly` | ✅ |
| 10 | Get Category by ID | GET | `/social-media-posting/{locationId}/categories/{id}` | -- | ✅ |
| | **Tags** | | | | |
| 11 | Get Tags | GET | `/social-media-posting/{locationId}/tags` | -- | ✅ |
| 12 | Get Tags by IDs | POST | `/social-media-posting/{locationId}/tags/details` | -- | ✅ |
| | **Statistics** | | | | |
| 13 | Get Statistics | POST | `/social-media-posting/statistics` | -- | ✅ |
| | **OAuth - Google** | | | | |
| 14 | Start Google OAuth | GET | `/social-media-posting/oauth/google/start` | `socialplanner/oauth.readonly` | 🔬 |
| 15 | Get Google Locations | GET | `/social-media-posting/oauth/{locationId}/google/locations/{accountId}` | -- | 🔬 |
| 16 | Set Google Locations | POST | `/social-media-posting/oauth/{locationId}/google/locations/{accountId}` | -- | 🔬 |
| | **OAuth - Facebook** | | | | |
| 17 | Start Facebook OAuth | GET | `/social-media-posting/oauth/facebook/start` | -- | ✅ |
| 18 | Get Facebook Pages | GET | `/social-media-posting/oauth/{locationId}/facebook/accounts/{accountId}` | -- | 🔬 |
| 19 | Attach Facebook Pages | POST | `/social-media-posting/oauth/{locationId}/facebook/accounts/{accountId}` | -- | 🔬 |
| | **OAuth - Instagram** | | | | |
| 20 | Start Instagram OAuth | GET | `/social-media-posting/oauth/instagram/start` | `socialplanner/oauth.readonly` | 🔬 |
| 21 | Get Instagram Accounts | GET | `/social-media-posting/oauth/{locationId}/instagram/accounts/{accountId}` | -- | 🔬 |
| 22 | Attach Instagram Accounts | POST | `/social-media-posting/oauth/{locationId}/instagram/accounts/{accountId}` | -- | 🔬 |
| | **OAuth - LinkedIn** | | | | |
| 23 | Start LinkedIn OAuth | GET | `/social-media-posting/oauth/linkedin/start` | -- | 🔬 |
| 24 | Get LinkedIn Pages/Profile | GET | `/social-media-posting/oauth/{locationId}/linkedin/accounts/{accountId}` | -- | 🔬 |
| 25 | Attach LinkedIn Pages/Profile | POST | `/social-media-posting/oauth/{locationId}/linkedin/accounts/{accountId}` | -- | 🔬 |
| | **OAuth - Twitter** | | | | |
| 26 | Start Twitter OAuth | GET | `/social-media-posting/oauth/twitter/start` | `socialplanner/oauth.readonly` | 🔬 |
| 27 | Get Twitter Profile | GET | `/social-media-posting/oauth/{locationId}/twitter/accounts/{accountId}` | -- | 🔬 |
| 28 | Attach Twitter Profile | POST | `/social-media-posting/oauth/{locationId}/twitter/accounts/{accountId}` | -- | 🔬 |
| | **OAuth - TikTok** | | | | |
| 29 | Start TikTok OAuth | GET | `/social-media-posting/oauth/tiktok/start` | `socialplanner/oauth.readonly` | 🔬 |
| 30 | Get TikTok Profile | GET | `/social-media-posting/oauth/{locationId}/tiktok/accounts/{accountId}` | -- | 🔬 |
| 31 | Attach TikTok Profile | POST | `/social-media-posting/oauth/{locationId}/tiktok/accounts/{accountId}` | -- | 🔬 |
| 32 | Start TikTok Business OAuth | GET | `/social-media-posting/oauth/tiktok-business/start` | `socialplanner/oauth.readonly` | 🔬 |
| 33 | Get TikTok Business Profile | GET | `/social-media-posting/oauth/{locationId}/tiktok-business/accounts/{accountId}` | -- | 🔬 |
| | **CSV Import** | | | | |
| 34 | Upload CSV | POST | `/social-media-posting/{locationId}/csv` | `socialplanner/csv.write` | 🔬 |
| 35 | Get Upload Status | GET | `/social-media-posting/{locationId}/csv` | -- | ✅ |
| 36 | Set Accounts | POST | `/social-media-posting/{locationId}/set-accounts` | `socialplanner/csv.write` | 🔬 |
| 37 | Get CSV Post | GET | `/social-media-posting/{locationId}/csv/{id}` | -- | 🔬 |
| 38 | Start CSV Finalize | PATCH | `/social-media-posting/{locationId}/csv/{id}` | -- | 🔬 |
| 39 | Delete CSV | DELETE | `/social-media-posting/{locationId}/csv/{id}` | -- | 🔬 |
| 40 | Delete CSV Post | DELETE | `/social-media-posting/{locationId}/csv/{csvId}/post/{postId}` | -- | 🔬 |
