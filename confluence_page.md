# Weekly Summary — AI Productivity Tool for PMs

**Status:** Live
**Repo:** https://github.com/himankinis/weekly-summary
**Owner:** Himankini Shah

---

## What is it?

Weekly Summary is a local-first weekly work summarizer built as part of the **AI Hacks for PMs** initiative. It replaces the manual "what did I do this week?" struggle by automatically collecting your work activity throughout the week — from Jira, Outlook email, calendar, and Claude Code — and generating a structured summary on demand.

All data stays on your machine (`~/.weekly-pulse/weekly-pulse.db`) — nothing is sent to any external server.

---

## How it works

Weekly Summary collects entries from five sources:

| Source | How it's captured |
|---|---|
| **Manual** | Type highlights, lowlights, or blockers directly in the dashboard |
| **Jira** | Synced via Jira REST API — resolved/updated tickets appear as highlights |
| **Confluence** | Synced via Confluence REST API — pages you created or edited |
| **Outlook email** | Imported from an Outlook JSON export — sent emails appear as highlights |
| **Calendar** | Pulled from your calendar ICS feed and classified as meetings |
| **Claude Code activity** | Auto-captured via a hook that fires on every prompt you submit to Claude Code |

At week's end, one click generates a formatted summary with the right level of detail for any audience.

---

## Entry types

| Type | Meaning |
|---|---|
| ✅ Highlight | Accomplishment, shipped work, good decision |
| ⚠️ Lowlight | Delay, missed target, thing that took longer than expected |
| 🚫 Blocker | Dependency, access issue, waiting on others |

---

## Setup

**Prerequisites:** Node.js 20+, npm

```bash
# 1. Clone the repo
git clone https://github.com/himankinis/weekly-summary
cd weekly-summary

# 2. Install dependencies
npm install

# 3. Configure integrations (copy and fill in your credentials)
cp .env.example .env

# 4. Register the Claude Code hook (auto-captures your prompts)
npm run setup

# 5. Start the dashboard
npm run dev
```

Open **http://localhost:3000** — the dashboard is ready.

> The server must be running for Claude Code activity to be auto-captured. Start a new Claude Code session after running `npm run setup` for the hook to take effect.

### Environment variables

| Variable | Description |
|---|---|
| `JIRA_BASE_URL` | Your Jira instance URL (e.g. `https://yourorg.atlassian.net`) |
| `CONFLUENCE_URL` | Your Confluence instance URL |
| `ATLASSIAN_EMAIL` | Your Atlassian account email |
| `JIRA_API_TOKEN` | API token from [id.atlassian.com](https://id.atlassian.com/manage-profile/security/api-tokens) |

---

## Using the dashboard

### Logging entries
- **Log an entry** — type a note and tag it as highlight / lowlight / blocker
- **Claude Activity** — auto-captured Claude Code prompts appear in a dedicated section (expandable to see the raw prompt); excluded from the generated summary

### Syncing data
- **Jira & Confluence** — click "Sync Jira & Confluence" to pull this week's tickets and pages
- **Email** — click "Sync Email" to import from your Outlook JSON export
- **Calendar** — paste your ICS URL in settings to sync meetings

### Viewing entries
Entries can be grouped two ways using the toggle in the log card:
- **By type** — Highlights / Lowlights / Blockers / Claude Activity
- **By source** — Manual / Jira / Confluence / Email / Claude

Each source has a color-coded pill badge: green (Jira), blue (Calendar), purple (Email), orange (Claude), gray (Manual/Confluence).

### Stats and trends
The dashboard header strip shows counts for highlights, lowlights, blockers, meetings, and auto-captured entries. A stacked progress bar below shows the highlight/lowlight/blocker ratio for the week, with a trend comparison against the previous week (e.g. "↑ 3 highlights · ↓ 1 blocker vs last week").

### Navigating weeks
Use the week arrows in the header to review any past week's log and summary.

---

## Generating a summary

Click **"Generate Summary"** in the right panel. The summary is cached and can be regenerated at any time.

### Audience formats

The summary supports four audience formats — switch using the dropdown in the summary card:

| Format | Contents | Best for |
|---|---|---|
| **PPM Weekly** | Markdown table of highlights + blockers, paste-ready | PPM Weekly Highlights doc |
| **For stakeholders** | Narrative opening · highlights with bold topics · Jira/email rollups · Relevant Sources | Leadership updates, status emails |
| **For 1:1** | Everything in stakeholders + Key Decisions + Next Week Preview | Weekly 1:1 with manager |
| **For myself** | Full detail — all sections including meetings with Jira enrichment | Personal record |

Each format has a **Copy** button that produces clean plain text for Slack, Teams, or a doc.

### What the summary includes

- **Quantitative line** — "This week: 5 highlights · 0 lowlights · 0 blockers | 13 meetings | 3 Jira tickets | 32 emails"
- **Narrative** — plain-English paragraph with top theme, key wins, and meeting count
- **Highlights, Lowlights, Blockers** — from manual + synced sources
- **Key Decisions** — entries and meetings containing decision keywords (aligned, decided, approved, confirmed, etc.)
- **Meetings** — from calendar, enriched with related Jira ticket entries where keys match
- **Next Week Preview** — upcoming calendar meetings + items carrying over from blockers/lowlights

### Past Summaries

A **Past Summaries** panel at the bottom of the right column shows all weeks with saved summaries. Click any week to expand and view the summary in whichever audience format is selected. Each row includes a Copy button.

### Terminal report

```bash
npm run report
```

Prints a plain-text summary to the terminal — useful for quick reference without opening the browser.

---

## Tech stack

| Layer | Technology |
|---|---|
| Framework | Next.js 15 (App Router) |
| Database | SQLite via `better-sqlite3` at `~/.weekly-pulse/` |
| UI | shadcn/ui + Tailwind CSS |
| Language | TypeScript (strict) |
| Integrations | Jira REST API, Confluence REST API, Outlook JSON export, ICS calendar feeds |

---

*Built as part of AI Hacks for PMs · Data is local-only and never synced*
