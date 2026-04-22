# URL Collection Storage Schema

## Database Location

`url-summary/urls.db` — a SQLite database relative to the workspace root.

## Schema

```sql
CREATE TABLE IF NOT EXISTS entries (
    id    INTEGER PRIMARY KEY AUTOINCREMENT,
    url   TEXT    NOT NULL UNIQUE,
    title TEXT,
    added TEXT    NOT NULL   -- ISO-8601 UTC timestamp, e.g. '2026-04-20T14:30:00Z'
);

CREATE INDEX IF NOT EXISTS idx_entries_added ON entries(added);
```

## Rules

| Rule | Detail |
|------|--------|
| Uniqueness | `url` must be unique within `entries`. The UNIQUE constraint enforces this. |
| Ordering | Entries are stored in insertion order via the auto-incrementing `id`. |
| Timestamps | Always UTC, always ISO-8601 with seconds precision. |
| Database creation | If `urls.db` does not exist, create it and run the schema above. |
| Index | The `idx_entries_added` index on `added` enables fast time-window filtering. |

## Interacting with the Database

Use the **Shell** tool to run `sqlite3` commands against the database. Examples:

### Insert a new entry

```bash
sqlite3 url-summary/urls.db "INSERT INTO entries (url, title, added) VALUES ('https://example.com', 'Example', '2026-04-20T14:30:00Z');"
```

### Query entries within a time window

```bash
sqlite3 url-summary/urls.db "SELECT url, title, added FROM entries WHERE added >= '2026-04-13T00:00:00Z' ORDER BY added DESC;"
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
SELECT url, title, added
FROM entries
WHERE added >= :cutoff
ORDER BY added DESC;
```
