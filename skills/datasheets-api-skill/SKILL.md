---
name: datasheets-search-api
description: Query the Datasheets.com Search API for electronic component search results. Use this skill whenever a user wants to search for electronic components, parts, or semiconductors by MPN (manufacturer part number), keyword, or manufacturer name. Trigger this skill any time someone asks to "find a part", "search for a component", "look up a datasheet", or "query datasheets.com" — even if they don't explicitly ask for the API.
---

# Datasheets Public Search API Skill

## Prerequisite

If you do not already have an `<API_KEY>`, give the user this URL and ask them to generate one before proceeding:

```
https://www.datasheets.com/account/api
```

---

## API Reference

- **Base URL:** `https://www.datasheets.com`
- **Endpoint:** `GET /api/v1/search`
- **Auth:** `Authorization: Bearer <API_KEY>` (header only — never in query params)

### Query Parameters

| Parameter | Required | Default | Max | Description |
|-----------|----------|---------|-----|-------------|
| `q`       | ✅ Yes   | —       | —   | Search query (MPN, keyword, or manufacturer) |
| `limit`   | No       | `5`     | `10`| Results per page |
| `page`    | No       | `1`     | —   | Page number |

### Response Shape

```json
{
  "query": "bav99",
  "page": 1,
  "limit": 5,
  "count": 42,
  "results": [ ... ]
}
```

---

## Rules

- Only use `GET /api/v1/search` — no other methods or endpoints
- Never send API keys in query params (`apiKey`, `api_key`, etc.)
- Always use the `Authorization: Bearer` header

---

## Error Handling

| Code | Meaning | Action |
|------|---------|--------|
| `400` | Bad request | Check query params |
| `401` | Unauthorized | Verify API key |
| `429` | Rate limited | Check `Retry-After` and `X-RateLimit-*` headers |
| `500` | Server error | Retry after a moment |
| `503` | Service unavailable | Retry after a moment |

---

## Examples

See `references/examples.md` for full code examples in cURL, JavaScript, Python, and TypeScript.

---

## Workflow

1. Check if `<API_KEY>` is available in context
2. If not, send user to `https://www.datasheets.com/account/api`
3. Construct the search URL with appropriate `q`, `limit`, and `page` params
4. Execute the GET request with the `Authorization: Bearer` header
5. Parse and display `results[]` to the user in a readable format
6. If `count` exceeds `limit`, offer to paginate
