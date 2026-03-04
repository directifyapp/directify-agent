# Directify Agent for OpenClaw

Manage your Directify directory through natural conversation. Create listings, organize categories, bulk import data, and more - all from your chat.

## What Can It Do?

**Listings**
- "Add a new listing called Acme Corp in the Software category"
- "Import these 50 listings from my CSV"
- "Update the description for listing #42"
- "Show me all draft listings"
- "Delete all listings in the Test category"

**Categories**
- "Create categories for Food, Drinks, and Entertainment"
- "Rename 'Restaurants' to 'Dining'"
- "Show me all categories with their listing counts"

**Tags**
- "Add tags: featured, new, popular"
- "Tag listings #10, #15, #20 as featured"

**Custom Fields**
- "Add a 'Price' number field and make it filterable"
- "Create a 'Size' dropdown with options: Small, Medium, Large"
- "Show me all custom fields"

**Bulk Operations**
- "Import this CSV of listings" (paste CSV data)
- "Set all listings in category 'Old' to draft"
- "Add the tag 'verified' to all published listings"

## Quick Start

### 1. Install OpenClaw

If you haven't already:
```bash
npm install -g openclaw
openclaw setup
```

### 2. Set Up the Agent

```bash
# Create workspace
mkdir -p ~/clawd-directify
cp -r ./* ~/clawd-directify/

# Copy OpenClaw config (skip if you already have one)
cp openclaw.example.json ~/.openclaw/openclaw.json
```

If you already have an `openclaw.json`, add this agent to your existing `agents.list`:

```json
{
    "id": "directify",
    "name": "Directify Agent",
    "workspace": "~/clawd-directify"
}
```

### 3. Configure Your API Key

Edit `~/clawd-directify/TOOLS.md` and fill in:

- **API Key** - Get it from your Directify dashboard > Settings > API
- **Directory ID** - Found in your dashboard URL or Settings > General

```markdown
- **Base URL:** `https://directify.app/api`
- **API Key:** `df_live_abc123...`
- **Directory ID:** `42`
```

### 4. Personalize (Optional)

Edit `USER.md` with your name and preferences:

```markdown
- **Name:** John
- **Directory:** Chicago Restaurants
- **URL:** https://chicagorestaurants.com
```

### 5. Start

```bash
openclaw gateway restart
```

Then message your agent:
> "Show me all my listings"

## File Structure

```
directify-agent/
├── README.md                    <- You're here
├── openclaw.example.json        <- OpenClaw gateway config
├── SOUL.md                      <- Agent personality
├── AGENTS.md                    <- Agent behavior rules
├── USER.md                      <- Your info (edit this)
├── TOOLS.md                     <- API config (edit this)
├── IDENTITY.md                  <- Agent identity
├── MEMORY.md                    <- Long-term memory (auto-updated)
├── HEARTBEAT.md                 <- Periodic checks
├── memory/                      <- Daily memory files
└── skills/
    └── directify-api/
        └── SKILL.md             <- Full API reference
```

## Multi-Agent Setup

If you're running multiple OpenClaw agents, add the Directify agent alongside your existing ones:

```json
{
    "agents": {
        "list": [
            {
                "id": "main",
                "name": "My Main Agent",
                "workspace": "~/clawd",
                "subagents": {
                    "allowAgents": ["directify"]
                }
            },
            {
                "id": "directify",
                "name": "Directify Agent",
                "workspace": "~/clawd-directify"
            }
        ]
    }
}
```

Your main agent can then delegate directory tasks:
> "Ask the Directify agent to add 10 new restaurant listings"

## Using with Telegram / Discord / Signal

The agent works on any channel OpenClaw supports. Configure your preferred channel in `openclaw.json`:

```json
{
    "channels": {
        "telegram": {
            "enabled": true,
            "botToken": "YOUR_BOT_TOKEN",
            "allowFrom": ["YOUR_USER_ID"]
        }
    }
}
```

## Examples

### Import from CSV

Paste your CSV directly in chat:
```
name,description,url,category_id
Acme Corp,Leading software company,https://acme.com,1
Beta Inc,Design agency,https://beta.design,2
Gamma LLC,Marketing firm,https://gamma.io,3
```

The agent will parse it, match categories, and create all listings.

### Bulk Update

> "Set all listings in 'Restaurants' category to published"

> "Add the custom field 'Rating' (number, filterable) and set it to 5 for all featured listings"

### Search and Report

> "How many listings do I have per category?"

> "Show me all listings without a website URL"

> "Find all draft listings created before January"

## API Rate Limits

- 60 requests per minute
- Bulk endpoints count as 1 request
- The agent automatically handles pagination for large datasets

## Troubleshooting

**"401 Unauthorized"**
Your API key is wrong or expired. Check TOOLS.md and regenerate from the Directify dashboard.

**"404 Not Found"**
Wrong directory ID. Check TOOLS.md.

**"422 Validation Error"**
Missing required fields. The agent will show you which fields need fixing.

**Agent doesn't know about my custom fields**
Tell it to fetch them: "List all my custom fields" - it will cache them for future use.

## Support

- Directify docs: https://docs.directify.app
- OpenClaw docs: https://docs.openclaw.ai
- Questions? support@directify.app
