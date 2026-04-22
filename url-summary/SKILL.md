---
name: url-summary
description: >-
  Collect and store URLs into a dated collection, then retrieve and summarize
  them over a time period. Use when the user wants to save URLs, add links to
  a collection, review saved URLs, summarize bookmarks, or get a topic overview
  of collected links.
---

# URL Summary

Manage a persistent collection of URLs with timestamps. Collect new URLs from the user, and on request, retrieve and summarize stored URLs for a given time window grouped by topic.

## Storage

URLs are stored in a SQLite database at `url-summary/urls.db` relative to the workspace root. See [storage.md](storage.md) for the full schema.

Each row in the `entries` table contains:

| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER | Auto-incrementing primary key |
| `url` | TEXT NOT NULL UNIQUE | The full URL including protocol |
| `title` | TEXT | Optional user-provided title (NULL if not given) |
| `added` | TEXT NOT NULL | ISO-8601 UTC timestamp, e.g. `2026-04-20T14:30:00Z` |

If the database does not exist, create it and initialize the schema:

```bash
sqlite3 url-summary/urls.db <<'SQL'
CREATE TABLE IF NOT EXISTS entries (
    id    INTEGER PRIMARY KEY AUTOINCREMENT,
    url   TEXT    NOT NULL UNIQUE,
    title TEXT,
    added TEXT    NOT NULL
);
CREATE INDEX IF NOT EXISTS idx_entries_added ON entries(added);
SQL
```

## Workflow: Add URLs

### Step 1 — Collect URLs

Ask the user for one or more URLs. Accept them as a list, comma-separated, or one per message. For each URL:

1. Validate that it looks like a well-formed URL (starts with `http://` or `https://`).
2. Optionally ask for a short title. If the user does not provide one, leave `title` as `null`.

### Step 2 — Persist

1. Open the `url-summary/urls.db` database (or create it and run the schema if missing).
2. Insert each new URL with `added` set to the current UTC ISO-8601 timestamp.
3. Confirm to the user how many URLs were added and list them.

## Workflow: Summarize URLs

When the user asks to review or summarize their saved URLs for a time period:

### Step 1 — Determine Time Window

Use AskQuestion if the user hasn't specified a period:

```
Prompt: "What time period would you like to review?"
Options:
  - Last week
  - Last month
  - Last year
  - Custom range
```

If "Custom range", ask for start and end dates conversationally.

Calculate the cutoff date from today's date and query `urls.db` for entries where `added >= cutoff`.

### Step 2 — Retrieve Content

For each URL in the filtered set:

1. Use the **WebFetch** tool to retrieve the page content.
2. If WebFetch fails (paywalled, 404, auth-required), note it as "could not retrieve" and skip.
3. Extract the key points from the fetched content — focus on the article's thesis, main arguments, and conclusions. Keep notes concise (3-5 bullet points per URL).

### Step 3 — Identify Topics

Analyze the extracted content across all retrieved URLs:

1. Identify the primary topic or theme for each URL.
2. Group URLs by shared topic.
3. For each topic, write a 1-2 sentence high-level summary of what the grouped URLs cover.

### Step 4 — Present the Summary

Use a Canvas when available (preferred for visual layout), otherwise format as markdown.

Present results in **descending order** from most-covered topic to least:

```
## URL Collection Summary — [time period]

**[count] URLs reviewed** | [count] could not be retrieved

### 1. [Topic Name] (X URLs)
[1-2 sentence summary of what these URLs collectively cover]

- [URL title or domain] — [one-line takeaway]
- [URL title or domain] — [one-line takeaway]

### 2. [Topic Name] (X URLs)
[1-2 sentence summary]

- [URL title or domain] — [one-line takeaway]

...
```

If only one URL exists for a topic, still list it under its own heading.

## Edge Cases

- **Empty collection**: If no URLs exist for the requested period, tell the user and suggest broadening the window.
- **Duplicate URLs**: When adding, the UNIQUE constraint on `url` prevents duplicates. If an INSERT fails due to a duplicate, ask the user whether to update the timestamp or skip it.
- **Large collections**: If more than 20 URLs match the time window, warn the user that retrieval may take a while and offer to limit to the most recent N.
