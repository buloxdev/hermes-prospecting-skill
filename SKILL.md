---
name: prospecting
description: Research and find prospects for sales outreach. Searches across web sources, company sites, directories, and news. Saves contacts to Notion and drafts personalized cold emails.
version: 1.1.0
author: Mike
---

# Prospecting Skill

Search for prospects across multiple web sources, filter by criteria, save to Notion, and draft personalized cold emails. Industry agnostic, configurable via environment variables and user preferences.

## Configuration

Before using this skill, set these in your `.env` or memory:

```
NOTION_API_KEY=your_notion_api_key
NOTION_PROSPECTS_DB_ID=your_database_id
INDUSTRY=your_industry            # e.g. Manufacturing, SaaS, Healthcare
TARGET_ROLES=VP,Director,Head of  # roles you search for
EXCLUDE_TYPES=logistics,transport  # company types to skip
REVENUE_MIN=100M                   # minimum revenue threshold
```

## Trigger

User says something like:
- "prospect [role] in [city]"
- "find me [title]s in [location]"
- "search for [function] executives in [metro area]"

## Workflow

### 1. Parse the request
Extract from user message:
- **Role/title** — VP, Director, Manager, Head of, C-level in the target function
- **City/location** — any city, metro area, or region (NEVER hardcode)
- **Industry focus** — if specified, otherwise use configured defaults

### 2. Search for prospects

Use these sources in order:

**A. Google X-ray search (primary)**
```
site:linkedin.com/in "[role]" "[function]" "[city]"
site:linkedin.com/in "[title]" "[city]" "[industry]"
```
Use web_search tool. Extract names, titles, companies from search snippets.

**B. Company-focused search**
```
"[city]" "[function]" "[role]" site:companywebsite.com
"[company]" leadership team [function]
```
Search for companies in the target city/industry, then find their leadership.

**C. Industry directories and databases**
- Crunchbase — search companies by location, industry, revenue
- Apollo.io — company and contact search
- Industry association member lists
- Trade publications and member directories

**D. Industry news sites (high-signal for exec appointments and company intel)**
- Search industry-specific news sites for the target vertical
- Exec appointments, expansions, M&A are all high-signal for prospecting

**E. News and press releases (general web)**
```
"[city]" "[function]" "appointed" OR "hired" OR "promoted" 2025..2026
```
Recent executive appointments are high-signal prospects.

**F. LinkedIn (ONLY when user explicitly asks or other sources exhausted)**
- Requires browser automation setup
- Send live URL to user for authentication
- Then search, extract, connect

### 3. Filter prospects

**INCLUDE:**
- Roles matching configured target roles (VP, SVP, Director, Senior Director, Head of, Chief)
- Companies matching target industry and size
- Revenue above configured threshold (verify via web search if uncertain)
- Location matching target city/metro area

**EXCLUDE:**
- Company types configured in EXCLUDE_TYPES
- Schools, universities, colleges
- Hospitals, healthcare systems
- Government agencies, nonprofits
- Consulting firms, staffing agencies, recruiters
- Private equity, investment firms
- Companies below revenue threshold (unless uncertain)

### 4. Save to Notion

Database ID from `NOTION_PROSPECTS_DB_ID` env var.
Notion API key from `NOTION_API_KEY` env var.
Use API version `2022-06-28` for all operations.

Properties to fill:
- **Company** (title): Company name
- **Contact Name** (rich_text): Full name
- **Contact Title** (rich_text): Their title/role
- **Status** (select): "New Lead"
- **Source** (select): "Web Research" (or "LinkedIn" if sourced from there)
- **Industry** (select): Match from configured options
- **Revenue** (select): Revenue bracket (research if possible)
- **Research Notes** (rich_text): Key intel, company background, why they fit
- **Website** (url): Company website if found

### 5. Draft personalized emails

Update each Notion page with a "Draft Email" (rich_text property).
Set status to "Email Drafted" after.

**Email rules:**
- NO em dashes (use commas, periods, or parentheses)
- Short subject line (under 10 words)
- Hook in first sentence (reference something specific about them or their company)
- Clear CTA (15-minute call or quick conversation)
- Sign off with your configured name
- Keep under 100 words
- Every email must feel human-written, no templates

### 6. Report back to user

Summarize what was found:
- Number of prospects found
- Company, name, title for each
- What was saved to Notion
- Any gaps (e.g., "could not verify revenue for X")

## Key rules

- NEVER hardcode a city. City comes from user request.
- NEVER use LinkedIn unless user explicitly says "use linkedin"
- NEVER guess contact info (emails, phone numbers)
- NEVER send emails. Draft only. User reviews in Notion.
- ALWAYS verify company type before adding
- When uncertain about revenue, note it in research notes rather than guessing
- Save to Notion FIRST, then draft emails, then update status
