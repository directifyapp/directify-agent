# Directify API Skill

Use this skill to manage a Directify directory via API. All endpoints require authentication.

Read TOOLS.md for the API key, base URL, and directory ID.

## Authentication

All requests must include:
```
Authorization: Bearer {API_KEY}
Content-Type: application/json
Accept: application/json
```

## Base URL

```
https://directify.app/api
```

---

## Directories

### List Directories
```
GET /directories
```

Returns all directories owned by the authenticated user.

Response:
```json
{
  "data": [
    { "id": 123, "name": "My Directory" }
  ]
}
```

---

## Listings

Listings use `/projects` in the URL path.

### List All Listings
```
GET /directories/{directory_id}/projects
```

Query params:
- `page` (int) - Page number (100 items per page)

Response includes `data` array and `meta` with pagination info.

### Get Single Listing
```
GET /directories/{directory_id}/projects/{project_id}
```

Returns full listing with all custom field values.

### Check Listing Exists
```
POST /directories/{directory_id}/projects/exists
```

Body:
```json
{
  "url": "https://example.com"
}
```

Returns 200 if the listing does not exist, 400 if it already exists.

### Create Listing
```
POST /directories/{directory_id}/projects
```

Body:
```json
{
  "name": "Listing Name",
  "description": "Short description",
  "content": "Full content (markdown/HTML)",
  "url": "https://example.com",
  "slug": "custom-slug",
  "email": "contact@example.com",
  "phone_number": "+1234567890",
  "address": "123 Main St, City, State, ZIP",
  "latitude": 40.7128,
  "longitude": -74.0060,
  "category_id": 1,
  "categories": [1, 2, 3],
  "tags": [1, 2],
  "is_active": true,
  "is_featured": false,
  "is_no_follow": false,
  "logo_url": "https://example.com/logo.png",
  "logo_alt_text": "Logo description",
  "image_url": "https://example.com/cover.jpg",
  "image_alt_text": "Image description",
  "additional_image_urls": ["https://example.com/photo1.jpg", "https://example.com/photo2.jpg"],
  "video_url": "https://youtube.com/embed/...",
  "social_links": {
    "twitter": "https://twitter.com/example",
    "github": "https://github.com/example"
  },
  "seo_title": "SEO Page Title",
  "seo_description": "SEO meta description",
  "schema": "{ JSON-LD schema }",
  "head_html": "<meta name='custom' content='value'>"
}
```

Required: `name`
Optional: everything else

Custom field values are passed as flat keys using the field name:
```json
{
  "name": "Bella Trattoria",
  "price_range": "2",
  "cuisine_type": "Italian, Pasta",
  "hours_of_operation": "Mon | 11:00 - 22:00\nTue | 11:00 - 22:00"
}
```

Use `GET /directories/{id}/custom-fields` to discover available field names.

### Update Listing
```
PUT /directories/{directory_id}/projects/{project_id}
```

Same body as create. Only include fields you want to update.

### Delete Listing
```
DELETE /directories/{directory_id}/projects/{project_id}
```

### Bulk Create Listings
```
POST /directories/{directory_id}/projects/bulk
```

Create up to 100 listings in a single request. Each listing is processed independently.

Body:
```json
{
  "listings": [
    {
      "name": "Listing 1",
      "description": "Description 1",
      "categories": [1],
      "tags": [1, 2]
    },
    {
      "name": "Listing 2",
      "url": "https://example2.com",
      "category_id": 2
    }
  ]
}
```

Response:
```json
{
  "data": [...],
  "errors": [
    {
      "index": 2,
      "name": "Failed Listing",
      "error": "Error description"
    }
  ],
  "total_created": 5,
  "total_errors": 1
}
```

---

## Categories

### List Categories
```
GET /directories/{directory_id}/categories
```

### Get Single Category
```
GET /directories/{directory_id}/categories/{category_id}
```

Response fields: `id`, `title`, `slug`, `description`, `content`, `icon`, `custom_icon_url`, `parent_id`, `parent_title`, `is_active`, `show_on_sidebar`, `order`, `seo_title`, `seo_description`, `head_html`, `created_at`, `updated_at`

### Create Category
```
POST /directories/{directory_id}/categories
```

Body:
```json
{
  "title": "Category Name",
  "slug": "category-name",
  "description": "Category description",
  "content": "Rich content (markdown)",
  "icon": "emoji-or-icon",
  "custom_icon_url": "https://example.com/icon.png",
  "parent_id": null,
  "order": 1,
  "is_active": true,
  "show_on_sidebar": true,
  "seo_title": "SEO Title",
  "seo_description": "SEO Description",
  "head_html": "<meta ...>"
}
```

Required: `title`

### Update Category
```
PUT /directories/{directory_id}/categories/{category_id}
```

Same fields as create, all optional.

### Delete Category
```
DELETE /directories/{directory_id}/categories/{category_id}
```

---

## Tags

### List Tags
```
GET /directories/{directory_id}/tags
```

### Get Single Tag
```
GET /directories/{directory_id}/tags/{tag_id}
```

Response fields: `id`, `title`, `slug`, `is_active`, `icon`, `heroicon`, `color`, `text_color`, `show_title`, `show_icon`, `created_at`, `updated_at`

### Create Tag
```
POST /directories/{directory_id}/tags
```

Body:
```json
{
  "title": "Tag Name",
  "slug": "tag-name",
  "color": "#f59e0b",
  "text_color": "#ffffff",
  "icon": "icon-name",
  "heroicon": "heroicon-o-star",
  "is_active": true,
  "show_title": true,
  "show_icon": true
}
```

Required: `title`

### Update Tag
```
PUT /directories/{directory_id}/tags/{tag_id}
```

### Delete Tag
```
DELETE /directories/{directory_id}/tags/{tag_id}
```

---

## Custom Fields

### List Custom Fields
```
GET /directories/{directory_id}/custom-fields
```

Returns all custom fields for the directory. Use the field `name` as the key when setting values on listings.

Note: Custom fields can only be created/updated/deleted from the Directify dashboard. The API only supports reading them and setting values on listings.

---

## Articles (Blog)

### List Articles
```
GET /directories/{directory_id}/articles
```

Query params:
- `page` (int) - Page number (100 items per page)

Returns paginated articles ordered by `published_at` descending.

### Get Single Article
```
GET /directories/{directory_id}/articles/{article_id}
```

Response fields: `id`, `title`, `slug`, `content`, `markdown`, `is_markdown`, `active`, `published_at`, `thumbnail_url`, `thumbnail_alt_text`, `categories`, `seo`, `user`, `directory`, `article_url`, `created_at`, `updated_at`

### Create Article
```
POST /directories/{directory_id}/articles
```

Body:
```json
{
  "title": "Article Title",
  "slug": "article-slug",
  "content": "<p>HTML content</p>",
  "markdown": "# Markdown content\n\nAlternative to HTML content.",
  "active": true,
  "published_at": "2026-04-01",
  "thumbnail_url": "https://example.com/thumb.jpg",
  "thumbnail_alt_text": "Image description",
  "categories": ["Reviews", "Italian"],
  "seo": {
    "title": "SEO Title",
    "description": "SEO Description"
  }
}
```

Required: `title` and either `content` or `markdown`
Note: `categories` are strings (names), not IDs. Slug is auto-generated from title if not provided.

### Update Article
```
PUT /directories/{directory_id}/articles/{article_id}
```

Same fields as create, all optional. Additional SEO fields available on update:
```json
{
  "seo": {
    "title": "SEO Title",
    "description": "SEO Description",
    "keywords": "keyword1, keyword2",
    "og_title": "Open Graph Title",
    "og_description": "OG Description",
    "og_image": "https://example.com/og.jpg",
    "twitter_title": "Twitter Title",
    "twitter_description": "Twitter Description",
    "twitter_image": "https://example.com/tw.jpg",
    "canonical_url": "https://example.com/canonical",
    "robots": "index, follow"
  }
}
```

### Delete Article
```
DELETE /directories/{directory_id}/articles/{article_id}
```

### Toggle Article Status
```
PATCH /directories/{directory_id}/articles/{article_id}/toggle
```

Toggles between active and inactive. No request body needed.

### Check Article Exists
```
POST /directories/{directory_id}/articles/exists
```

Body:
```json
{
  "slug": "article-slug"
}
```

Returns 200 if the article does not exist, 400 if it already exists.

### Get Articles by Category
```
GET /directories/{directory_id}/articles/category/{category_name}
```

Returns paginated articles filtered by category name.

---

## Pages (Custom Pages)

Custom pages are standalone pages for static content (About, Terms, comparisons) or programmatic SEO pages. They appear at `/pages/{slug}/` on the directory site.

### List Pages
```
GET /directories/{directory_id}/pages
```

Returns paginated pages ordered by `order`.

### Get Single Page
```
GET /directories/{directory_id}/pages/{page_id}
```

Response fields: `id`, `title`, `slug`, `markdown`, `placement`, `is_markdown`, `is_published`, `is_external`, `external_url`, `new_tab`, `order`, `seo`, `directory`, `page_url`, `created_at`, `updated_at`

### Create Page
```
POST /directories/{directory_id}/pages
```

Body:
```json
{
  "title": "Page Title",
  "slug": "page-slug",
  "markdown": "# Page content\n\nMarkdown content for the page.",
  "placement": "unlisted",
  "is_published": true,
  "order": 0,
  "seo": {
    "title": "SEO Title",
    "description": "SEO Description"
  }
}
```

Required: `title`

Fields:
- `title` (string) - Page title
- `slug` (string) - URL slug, auto-generated from title if not provided
- `markdown` (string) - Page content in markdown
- `placement` (string) - Where the page link appears: `navbar`, `footer`, `sidebar`, or `unlisted` (default)
- `is_published` (boolean) - Published status (default: true)
- `order` (integer) - Sort order in navigation (default: 0)
- `is_external` (boolean) - If true, this is a link to an external URL, not a page
- `external_url` (string) - External URL (required if `is_external` is true)
- `new_tab` (boolean) - Open external link in new tab
- `seo` (object) - SEO metadata with `title` and `description`

Uses `updateOrCreate` based on slug — creating a page with an existing slug updates it instead.

### Update Page
```
PUT /directories/{directory_id}/pages/{page_id}
```

Same fields as create, all optional. If title changes and no slug provided, slug is auto-generated.

### Delete Page
```
DELETE /directories/{directory_id}/pages/{page_id}
```

### Toggle Page Status
```
PATCH /directories/{directory_id}/pages/{page_id}/toggle
```

Toggles between published and unpublished. No request body needed.

---

## Common Patterns

### Add a listing with custom fields
```bash
curl -X POST https://directify.app/api/directories/{id}/projects \
  -H "Authorization: Bearer {key}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Bella Trattoria",
    "description": "Authentic Italian cuisine",
    "categories": [5],
    "tags": [1, 3],
    "address": "123 Main St, New York",
    "price_range": "2",
    "cuisine_type": "Italian, Pasta",
    "hours_of_operation": "Mon | 11:00 - 22:00\nTue | 11:00 - 22:00\nWed | 11:00 - 22:00",
    "url": "https://bellatrattoria.com",
    "is_active": true
  }'
```

### Publish a blog article
```bash
curl -X POST https://directify.app/api/directories/{id}/articles \
  -H "Authorization: Bearer {key}" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Top 10 Italian Restaurants",
    "markdown": "# Top 10\n\n1. Bella Trattoria\n2. ...",
    "categories": ["Reviews", "Italian"],
    "active": true,
    "seo": {
      "title": "Top 10 Italian Restaurants in NYC",
      "description": "Discover the best Italian restaurants..."
    }
  }'
```

### Bulk import from CSV
When user provides CSV data:
1. Parse the CSV
2. Map columns to API fields (`name`, `description`, `url`, `email`, etc.)
3. Use the bulk create endpoint (`/projects/bulk`)
4. Report success/failure count

### Fetch all listings (paginated)
```bash
curl "https://directify.app/api/directories/{id}/projects?page=1" \
  -H "Authorization: Bearer {key}"
```

Loop through pages until you get fewer than 100 results.

---

## Error Handling

- `401` - Invalid or missing API key
- `403` - Insufficient permissions (you don't own this directory)
- `404` - Resource not found
- `422` - Validation error (check response body for field-level errors)
- `429` - Rate limited (wait and retry)

## Rate Limits

- **120 requests per minute** per directory
- Bulk endpoints count as 1 request regardless of item count

## Tips for the Agent

1. Always read TOOLS.md first for API credentials
2. Use `exec` with `curl` to make API calls
3. Parse JSON responses with `jq` or inline
4. For bulk operations, batch into groups of 100
5. Always confirm before bulk deletes
6. Cache the directory ID and category list to avoid repeated lookups
7. When creating listings, fetch custom fields first to know which extra fields are available
8. Use the `exists` endpoint to check for duplicates before creating
9. For articles, prefer `markdown` over `content` (HTML) for easier content creation
10. Article categories are strings (names), not IDs — unlike listing categories which use IDs
