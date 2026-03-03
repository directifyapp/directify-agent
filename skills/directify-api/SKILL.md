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
https://directify.app/api/v1
```

---

## Directories

### List Directories
```
GET /directories
```

### Get Directory Details
```
GET /directories/{directory_id}
```

---

## Listings

### List All Listings
```
GET /directories/{directory_id}/listings
```

Query params:
- `page` (int) - Page number
- `per_page` (int) - Items per page (default 15, max 100)
- `search` (string) - Search by title
- `category_id` (int) - Filter by category
- `tag_id` (int) - Filter by tag
- `status` (string) - Filter by status: `published`, `draft`, `pending`
- `sort` (string) - Sort by: `title`, `created_at`, `updated_at`
- `order` (string) - `asc` or `desc`

### Get Single Listing
```
GET /directories/{directory_id}/listings/{listing_id}
```

### Create Listing
```
POST /directories/{directory_id}/listings
```

Body:
```json
{
    "title": "Listing Name",
    "description": "Short description",
    "content": "Full HTML or text content",
    "status": "published",
    "category_id": 1,
    "tags": [1, 2, 3],
    "custom_fields": {
        "field_slug": "value",
        "another_field": "value"
    },
    "website": "https://example.com",
    "email": "contact@example.com",
    "phone": "+1234567890",
    "address": "123 Main St, City, State, ZIP",
    "latitude": 41.8781,
    "longitude": -87.6298
}
```

Required: `title`
Optional: everything else

### Update Listing
```
PUT /directories/{directory_id}/listings/{listing_id}
```

Same body as create. Only include fields you want to update.

### Delete Listing
```
DELETE /directories/{directory_id}/listings/{listing_id}
```

### Bulk Create Listings
```
POST /directories/{directory_id}/listings/bulk
```

Body:
```json
{
    "listings": [
        {
            "title": "Listing 1",
            "description": "Description 1",
            "category_id": 1
        },
        {
            "title": "Listing 2",
            "description": "Description 2",
            "category_id": 2
        }
    ]
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
    "name": "Category Name",
    "description": "Category description",
    "parent_id": null,
    "order": 1,
    "icon": "icon-name"
}
```

Required: `name`

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

### Create Tag
```
POST /directories/{directory_id}/tags
```

Body:
```json
{
    "name": "Tag Name"
}
```

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

### Create Custom Field
```
POST /directories/{directory_id}/custom-fields
```

Body:
```json
{
    "name": "Field Name",
    "slug": "field_name",
    "type": "text",
    "required": false,
    "options": [],
    "placeholder": "Enter value...",
    "help_text": "Help text shown to users",
    "order": 1,
    "filterable": true,
    "searchable": true
}
```

Field types: `text`, `textarea`, `number`, `select`, `multiselect`, `checkbox`, `url`, `email`, `phone`, `date`, `color`

For `select` and `multiselect`, provide options:
```json
{
    "type": "select",
    "options": ["Option 1", "Option 2", "Option 3"]
}
```

### Update Custom Field
```
PUT /directories/{directory_id}/custom-fields/{field_id}
```

### Delete Custom Field
```
DELETE /directories/{directory_id}/custom-fields/{field_id}
```

---

## Common Patterns

### Add a listing with custom fields
```bash
curl -X POST https://directify.app/api/v1/directories/{id}/listings \
  -H "Authorization: Bearer {key}" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Acme Corp",
    "description": "Leading software company",
    "category_id": 5,
    "tags": [1, 3],
    "custom_fields": {
      "pricing": "$49/mo",
      "founded": "2020",
      "employees": "50-100"
    },
    "website": "https://acme.com",
    "status": "published"
  }'
```

### Bulk import from CSV
When user provides CSV data:
1. Parse the CSV
2. Map columns to API fields
3. Use the bulk create endpoint
4. Report success/failure count

### Search and filter
```bash
curl "https://directify.app/api/v1/directories/{id}/listings?search=acme&category_id=5&status=published&per_page=50" \
  -H "Authorization: Bearer {key}"
```

---

## Error Handling

- `401` - Invalid or missing API key
- `403` - Insufficient permissions
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
4. For bulk operations, batch into groups of 50
5. Always confirm deletes with the user
6. Cache the directory ID and category list to avoid repeated lookups
7. When creating listings, try to auto-assign categories based on context
