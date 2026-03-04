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

---

## Listings

Listings use `/projects` in the URL path.

### List All Listings
```
GET /directories/{directory_id}/projects
```

Query params:
- `page` (int) - Page number (100 items per page)

### Get Single Listing
```
GET /directories/{directory_id}/projects/{project_id}
```

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
    "content": "Full content or HTML",
    "url": "https://example.com",
    "email": "contact@example.com",
    "phone_number": "+1234567890",
    "address": "123 Main St, City, State, ZIP",
    "category_id": 1,
    "categories": [1, 2, 3],
    "tags": [1, 2],
    "is_active": true,
    "is_featured": false,
    "is_no_follow": false,
    "logo_url": "https://example.com/logo.png",
    "image_url": "https://example.com/cover.jpg",
    "video_url": "https://youtube.com/watch?v=...",
    "social_links": {
        "twitter": "https://twitter.com/example",
        "github": "https://github.com/example"
    }
}
```

Required: `name` (or `url` — if only URL is provided, info is auto-pulled from the page)
Optional: everything else

Custom field values are passed as flat keys using the field name (e.g., `"pricing": "Free"`, `"founded_year": "2020"`).

When a `url` is provided, the API uses it for duplicate detection — if a listing with the same URL exists, the existing one is returned.

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

Create up to 100 listings in a single request. Each listing is processed independently — if one fails, the rest still create.

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
            "description": "Description 2",
            "url": "https://example2.com",
            "category_id": 2
        }
    ]
}
```

Each item in `listings` accepts the same fields as the single create endpoint. `name` is required for each listing.

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
    "parent_id": null,
    "order": 1,
    "icon": "icon-name",
    "is_active": true,
    "show_on_sidebar": true
}
```

Required: `title`

### Update Category
```
PUT /directories/{directory_id}/categories/{category_id}
```

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

### Create Tag
```
POST /directories/{directory_id}/tags
```

Body:
```json
{
    "title": "Tag Name",
    "slug": "tag-name",
    "icon": "icon-name",
    "text_color": "#FFFFFF",
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

## Common Patterns

### Add a listing with custom fields
```bash
curl -X POST https://directify.app/api/directories/{id}/projects \
  -H "Authorization: Bearer {key}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Acme Corp",
    "description": "Leading software company",
    "categories": [5],
    "tags": [1, 3],
    "pricing": "$49/mo",
    "founded": "2020",
    "url": "https://acme.com",
    "is_active": true
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

---

## Error Handling

- `401` - Invalid or missing API key
- `403` - Insufficient permissions (you don't own this directory)
- `404` - Resource not found
- `422` - Validation error (check response body for field-level errors)
- `429` - Rate limited (wait and retry)

## Rate Limits

- 60 requests per minute per API key
- Bulk endpoints count as 1 request regardless of item count

## Tips for the Agent

1. Always read TOOLS.md first for API credentials
2. Use `exec` with `curl` to make API calls
3. Parse JSON responses with `jq` or inline
4. For bulk operations, batch into groups of 100
5. Always confirm deletes with the user
6. Cache the directory ID and category list to avoid repeated lookups
7. When creating listings, fetch custom fields first to know which extra fields are available
8. Use the `exists` endpoint to check for duplicates before creating
