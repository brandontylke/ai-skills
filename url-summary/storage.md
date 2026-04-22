# URL Collection Storage Schema

## File Location

`url-summary/urls.json` — relative to the workspace root.

## Schema

```json
{
  "entries": [
    {
      "url": "string (required) — the full URL including protocol",
      "title": "string | null — optional user-provided title",
      "added": "string (required) — ISO-8601 UTC timestamp, e.g. 2026-04-20T14:30:00Z"
    }
  ]
}
```

## Rules

| Rule | Detail |
|------|--------|
| Uniqueness | `url` must be unique within `entries`. Check before inserting. |
| Ordering | Entries are stored in insertion order (newest last). |
| Timestamps | Always UTC, always ISO-8601 with seconds precision. |
| File creation | If `urls.json` does not exist, create it with `{ "entries": [] }`. |
| File format | Pretty-printed JSON with 2-space indent for readability. |

## Time Window Filtering

When filtering by period, compute the cutoff from the current date:

| Period | Cutoff |
|--------|--------|
| Last week | today minus 7 days |
| Last month | today minus 30 days |
| Last year | today minus 365 days |
| Custom | user-supplied start/end dates |

Include entries where `added >= cutoff` (inclusive).
