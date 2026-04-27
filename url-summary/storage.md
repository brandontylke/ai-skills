# URL Collection Storage Schema

## Database Location

`url-summary/urls.db` — a SQLite database relative to the workspace root.

## Schema

```sql
CREATE TABLE IF NOT EXISTS entries (
    id      INTEGER PRIMARY KEY AUTOINCREMENT,
    url     TEXT    NOT NULL UNIQUE,
    title   TEXT,
    added   TEXT    NOT NULL,  -- ISO-8601 UTC timestamp, e.g. '2026-04-20T14:30:00Z'
    summary TEXT               -- Cached page summary (NULL until fetched)
);

CREATE INDEX IF NOT EXISTS idx_entries_added ON entries(added);
```

## Migration

If the database already exists but lacks the `summary` column, add it:

```bash
sqlite3 url-summary/urls.db "ALTER TABLE entries ADD COLUMN summary TEXT;"
```

This is safe to run even after the column exists — SQLite will return an error you can ignore.

After adding the column, back-fill summaries for existing rows. See the **Back-fill Summaries** workflow in SKILL.md.

## Rules

| Rule | Detail |
|------|--------|
| Uniqueness | `url` must be unique within `entries`. The UNIQUE constraint enforces this. |
| Ordering | Entries are stored in insertion order via the auto-incrementing `id`. |
| Timestamps | Always UTC, always ISO-8601 with seconds precision. |
| Summaries | Stored as plain text (3-5 bullet points). NULL means not yet fetched. |
| Database creation | If `urls.db` does not exist, create it and run the schema above. |
| Index | The `idx_entries_added` index on `added` enables fast time-window filtering. |

## Interacting with the Database

Use the **Shell** tool to run `sqlite3` commands against the database. Examples:

### Insert a new entry with summary

```bash
sqlite3 url-summary/urls.db "INSERT INTO entries (url, title, added, summary) VALUES ('https://example.com', 'Example', '2026-04-20T14:30:00Z', 'Key points from the page...');"
```

### Update the summary for an existing entry

```bash
sqlite3 url-summary/urls.db "UPDATE entries SET summary = 'Updated summary...' WHERE url = 'https://example.com';"
```

### Query entries within a time window

```bash
sqlite3 url-summary/urls.db "SELECT url, title, added, summary FROM entries WHERE added >= '2026-04-13T00:00:00Z' ORDER BY added DESC;"
```

### Find entries missing a summary

```bash
sqlite3 url-summary/urls.db "SELECT url, title FROM entries WHERE summary IS NULL;"
```

### Check for duplicates before inserting

```bash
sqlite3 url-summary/urls.db "SELECT COUNT(*) FROM entries WHERE url = 'https://example.com';"
```

## Time Window Filtering

When filtering by period, compute the cutoff from the current date:

| Period | Cutoff |
|--------|--------|
| Last week | today minus 7 days |
| Last month | today minus 30 days |
| Last year | today minus 365 days |
| Custom | user-supplied start/end dates |

Include entries where `added >= cutoff` (inclusive). Use the index on `added` for efficient filtering:

```sql
SELECT url, title, added, summary
FROM entries
WHERE added >= :cutoff
ORDER BY added DESC;
```
