# Prospecting Skill for Hermes

A research-driven sales prospecting skill that finds targets, saves them to Notion, and drafts personalized cold emails. Works with any industry, configured entirely through environment variables.

## What It Does

- Searches web sources, company sites, directories, and industry news for prospects
- Filters by role, revenue, location, and industry criteria
- Saves contacts and research notes to a Notion database
- Drafts personalized cold emails (never sends, always drafts for review)

## How It Works

1. User requests prospects (e.g. "find VP Supply Chain in Chicago")
2. Skill searches across multiple sources for matches
3. Filters against configured inclusion/exclusion rules
4. Saves qualified prospects to Notion with research notes
5. Drafts personalized cold emails in Notion for review

## Setup

### 1. Add the skill to your Hermes profile

Copy `SKILL.md` to your Hermes skills directory:

```bash
~/.hermes/profiles/YOUR_PROFILE/skills/prospecting/SKILL.md
```

### 2. Set environment variables

Add these to your profile's `.env` or configure via memory:

```
NOTION_API_KEY=your_notion_api_key
NOTION_PROSPECTS_DB_ID=your_database_id
INDUSTRY="Enter your target"
TARGET_ROLES=VP,Director,Head of
EXCLUDE_TYPES="Companies you dont want to inlcude in your list"
REVENUE_MIN=100M
```

### 3. Create your Notion prospects database

Required properties:

| Property | Type | Description |
|----------|------|-------------|
| Company | Title | Company name |
| Contact Name | Rich Text | Full name of prospect |
| Contact Title | Rich Text | Their role/title |
| Status | Select | Pipeline stage (New Lead, Email Drafted, etc.) |
| Source | Select | How they were found (Web Research, LinkedIn, etc.) |
| Industry | Select | Company industry vertical |
| Revenue | Select | Revenue bracket |
| Research Notes | Rich Text | Key intel and context |
| Website | URL | Company website |
| Draft Email | Rich Text | Personalized cold email draft |

## Trigger Examples

- "prospect VP Supply Chain in Dallas"
- "find me Directors of Sales in Chicago"
- "search for Finance executives in Atlanta"

## Search Sources (in order)

1. Google X-ray search (LinkedIn profiles via web search)
2. Company-focused search (leadership pages, about pages)
3. Industry directories (Crunchbase, trade associations)
4. Industry news sites (exec appointments, expansions, M&A)
5. Press releases and general news
6. LinkedIn (only when explicitly requested)

## Rules

- Never hardcode locations, always pull from user request
- Never guess contact info (emails, phone, social)
- Never send emails, draft only for user review
- Always verify company type before adding
- When revenue is uncertain, note it rather than guessing

## License

MIT
