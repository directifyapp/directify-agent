# TOOLS.md - Directify API Configuration

## API Setup

Update these values with your Directify API credentials:

- **Base URL:** `https://directify.app/api/v1`
- **API Key:** `YOUR_API_KEY_HERE`
- **Directory ID:** `YOUR_DIRECTORY_ID`

### How to get your API key:
1. Go to your Directify dashboard
2. Navigate to Settings > API
3. Generate a new API key
4. Copy it here

### How to get your Directory ID:
1. Go to your Directify dashboard
2. Navigate to Settings > General
3. Your Directory ID is shown in the URL or settings page

## API Headers

All requests use:
```
Authorization: Bearer YOUR_API_KEY_HERE
Content-Type: application/json
Accept: application/json
```
