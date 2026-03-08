# Cosense (Scrapbox) API Reference

Base URL: `https://scrapbox.io`

All requests require `Cookie: connect.sid=<SID>` for private projects and all write operations.

## Read Endpoints (GET)

### List Pages
```
GET /api/pages/:project?limit=100&skip=0&sort=updated
```
Sort options: `updated`, `created`, `accessed`, `linked`, `views`, `title`

Response: `{ projectName, skip, limit, count, pages: [{ id, title, descriptions, views, linked, created, updated, ... }] }`

### Get Page
```
GET /api/pages/:project/:title
```
Response includes `lines: [{ id, text, userId, created, updated }]`

URL-encode the title (e.g., spaces → `%20`).

### Get Page as Text
```
GET /api/pages/:project/:title/text
```
Returns plain text.

### Search
```
GET /api/pages/:project/search/query?q=keywords
```

### Get All Titles
```
GET /api/pages/:project/search/titles
```
Paginated via `followingId` parameter.

### Export Project
```
GET /api/page-data/export/:project.json
```

## Write Endpoints

### Get CSRF Token (required before writes)
```
GET /api/csrf-token
```
Returns `{ csrfToken: "..." }`. Send as `X-CSRF-TOKEN` header.

### Import (Create/Update Pages)
```
POST /api/page-data/import/:project.json
```
Headers: `Content-Type: application/json`, `X-CSRF-TOKEN: <token>`

Body:
```json
{
  "pages": [
    {
      "title": "Page Title",
      "lines": ["Page Title", "line 2", "line 3"]
    }
  ]
}
```

- First element of `lines` should match `title`
- If a page with the same title exists, it is **overwritten**
- Max 30MB per request

### Import Finish (optional)
```
POST /api/page-data/import-finish/:project.json
```

## Cosense Notation (記法)

Lines in Cosense use a bracket-based markup:

- `[link]` — internal link
- `[/project/page]` — cross-project link
- `[URL title]` or `[title URL]` — external link
- `[* bold]`, `[** larger bold]`
- `[/ italic]`
- `[-/ strikethrough italic]`
- `` `code` `` — inline code
- `code:filename` followed by indented lines — code block
- `table:name` followed by indented tab-separated lines — table
- `[URL.png]` or `[URL.jpg]` — image embed
- `>` quote
- Indentation with spaces/tabs creates hierarchy
- `#hashtag` — tag
- `[icon.icon]` — user icon
