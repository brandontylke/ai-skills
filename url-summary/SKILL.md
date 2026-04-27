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
| `summary` | TEXT | Cached page summary (NULL until fetched) |

If the database does not exist, create it and initialize the schema:

```bash
sqlite3 url-summary/urls.db <<'SQL'
CREATE TABLE IF NOT EXISTS entries (
    id      INTEGER PRIMARY KEY AUTOINCREMENT,
    url     TEXT    NOT NULL UNIQUE,
    title   TEXT,
    added   TEXT    NOT NULL,
    summary TEXT
);
CREATE INDEX IF NOT EXISTS idx_entries_added ON entries(added);
SQL
```

### Migration

If the database already exists but lacks the `summary` column, run:

```bash
sqlite3 url-summary/urls.db "ALTER TABLE entries ADD COLUMN summary TEXT;"
```

After adding the column, back-fill summaries for rows that have `summary IS NULL`. See the **Back-fill Summaries** workflow below.

## Workflow: Add URLs

### Step 1 — Collect URLs

Ask the user for one or more URLs. Accept them as a list, comma-separated, or one per message. For each URL:

1. Validate that it looks like a well-formed URL (starts with `http://` or `https://`).
2. Optionally ask for a short title. If the user does not provide one, leave `title` as `null`.

### Step 2 — Fetch & Summarize

For each URL being added:

1. Use the **WebFetch** tool to retrieve the page content.
2. If WebFetch fails (paywalled, 404, auth-required), set `summary` to `NULL` and note the failure.
3. If successful, extract 3-5 concise bullet points covering the article's thesis, main arguments, and conclusions. Store this as the `summary` value (plain text, one bullet per line prefixed with `- `).

### Step 3 — Persist

1. Open the `url-summary/urls.db` database (or create it and run the schema/migration if needed).
2. Insert each new URL with `added` set to the current UTC ISO-8601 timestamp and `summary` set to the fetched summary (or NULL on fetch failure).
3. Confirm to the user how many URLs were added and list them, noting any that could not be summarized.

## Workflow: Back-fill Summaries

Run this workflow when existing entries have `summary IS NULL` — typically after the migration adds the `summary` column.

1. Query for entries missing a summary:
   ```bash
   sqlite3 url-summary/urls.db "SELECT id, url FROM entries WHERE summary IS NULL;"
   ```
2. For each row returned:
   a. Use **WebFetch** to retrieve the page content.
   b. If WebFetch fails, leave `summary` as NULL and note the failure.
   c. If successful, extract 3-5 concise bullet points (same format as Add URLs Step 2).
   d. Update the row:
      ```bash
      sqlite3 url-summary/urls.db "UPDATE entries SET summary = '<summary text>' WHERE id = <id>;"
      ```
3. Report to the user how many entries were back-filled and how many could not be fetched.

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

### Step 2 — Use Saved Summaries (default) or Refresh

Query the matching entries including the `summary` column:

```sql
SELECT url, title, added, summary FROM entries WHERE added >= :cutoff ORDER BY added DESC;
```

**By default, use the saved `summary` column.** Before proceeding, ask the user whether they want to refresh the summaries:

```
Prompt: "Would you like to fetch updated summaries for these URLs, or use the saved ones?"
Options:
  - Use saved summaries (faster)
  - Fetch updated summaries from the web
```

- **Use saved summaries**: For each URL, use the `summary` column value. If `summary` is NULL for any entry, fetch it now with WebFetch and update the row.
- **Fetch updated summaries**: For each URL, use WebFetch to retrieve the page, generate a new 3-5 bullet point summary, and update the `summary` column in the database with the fresh content.

### Step 3 — Identify Topics

Analyze the summaries across all URLs:

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
