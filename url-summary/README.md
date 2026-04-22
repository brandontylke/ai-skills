# URL Summary

A skill that turns your AI assistant into a personal URL collector and research summarizer.

## Motivation

I always have too many tabs open. All with good intentions of getting back to them "when I have time." The goal of this skill is to solve that issue by having a central collection for those tabs. Something that will let me save the URLs, like a bookmark, but in a more useful way. I want to be able to ask "What topics have I been reading about lately?" or "What things have I been researching this year?" with the goal of spotting trends that would otherwise be lost.


<br/>
AI Generated, but valid, text here:

Tabs pile up, browsers slow down, and the things you actually wanted to remember get lost in the noise.

This skill solves that problem. Instead of leaving tabs open "for later," you drop the URLs into a dated collection managed by your agent. When you're ready, ask the agent to summarize what you've saved over the last week, month, or year. It fetches each page, extracts the key points, and groups everything by topic — ranked from most frequently covered to least. Over time, this builds a picture of your interest trends so you can spot recurring themes and decide what's worth investigating further.

Close the tabs. Keep the knowledge.

## What It Does

- **Collect URLs** — Hand the agent one or more URLs and it stores them with a timestamp in a local JSON file.
- **Summarize by time period** — Ask for a summary of your saved URLs from the last week, last month, last year, or a custom date range.
- **Topic grouping** — The agent fetches each page, identifies topics, and groups URLs by theme in descending order from most covered to least.
- **Trend spotting** — By reviewing summaries over different time windows, you can see which topics keep coming up and decide where to dig deeper.

## Usage

### Adding URLs

Tell the agent you want to save some URLs:

> "Save these URLs for me: https://example.com/article-one, https://example.com/article-two"

> "Add this link to my collection: https://example.com/interesting-post"

The agent validates each URL, checks for duplicates, and appends them to `url-summary/urls.json` with the current date.

### Reviewing and Summarizing

Ask the agent to review what you've collected:

> "Summarize my saved URLs from the last month"

> "What topics have I been reading about this year?"

> "Review my URL collection from last week"

The agent will:

1. Filter your collection to the requested time period.
2. Fetch and read each page.
3. Identify topics and group the URLs.
4. Present a summary ranked by topic frequency — most covered first.

## File Structure

```
url-summary/
├── SKILL.md       # Skill instructions for the Cursor agent
├── storage.md     # JSON schema and storage rules reference
├── README.md      # This file
└── urls.json      # Your URL collection (created automatically on first use)
```

## Storage Format

URLs are stored in `urls.json` as a simple JSON array of entries:

```json
{
  "entries": [
    {
      "url": "https://example.com/article",
      "title": "Optional title",
      "added": "2026-04-20T14:30:00Z"
    }
  ]
}
```

See [storage.md](storage.md) for the full schema and filtering rules.

## Installation

Place the `url-summary` folder in one of:

| Location | Scope |
|----------|-------|
| `~/.cursor/skills/url-summary/` | Available across all your projects |
| `~/.claude/skills/url-summary/` | Available across all your projects |
| `.cursor/skills/url-summary/` | Scoped to a single repository |

The agent will automatically pick up the skill and activate it when you mention saving or summarizing URLs.
