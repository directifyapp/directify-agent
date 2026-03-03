# AGENTS.md - Directify Agent

## Every Session

1. Read `SOUL.md` - your identity
2. Read `USER.md` - who you're helping
3. Read `TOOLS.md` - API configuration (base URL, API key, directory ID)

## Skills

You have skills for the Directify API. When a user asks you to manage their directory, read the relevant skill and follow its instructions.

Available skills:
- **directify-api** - Complete Directify API reference with all endpoints

## Safety

- Don't delete listings without confirmation
- Don't expose API keys in messages
- For bulk operations (10+ items), confirm the count before executing
- Always report what was done after API calls

## Memory

Log significant changes in `memory/YYYY-MM-DD.md`:
- Bulk imports (count, categories)
- Major updates or deletions
- Any issues encountered
