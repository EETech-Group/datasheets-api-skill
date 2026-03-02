# Datasheets.com API — Code Examples

## cURL

```bash
curl -sS "https://www.datasheets.com/api/v1/search?q=bav99&limit=10&page=1" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

---

## JavaScript (fetch)

```js
async function searchComponents(query, apiKey, limit = 5, page = 1) {
  const url = new URL('https://www.datasheets.com/api/v1/search');
  url.searchParams.set('q', query);
  url.searchParams.set('limit', limit);
  url.searchParams.set('page', page);

  const response = await fetch(url.toString(), {
    headers: {
      Authorization: `Bearer ${apiKey}`,
    },
  });

  if (!response.ok) {
    throw new Error(`API error: ${response.status}`);
  }

  const data = await response.json();
  return data; // { query, page, limit, count, results[] }
}

// Usage
const data = await searchComponents('bav99', 'YOUR_API_KEY', 10);
console.log(data.results);
```

---

## TypeScript

```ts
interface SearchResult {
  // Add fields as discovered from API responses
  [key: string]: unknown;
}

interface SearchResponse {
  query: string;
  page: number;
  limit: number;
  count: number;
  results: SearchResult[];
}

async function searchComponents(
  query: string,
  apiKey: string,
  limit = 5,
  page = 1
): Promise<SearchResponse> {
  const url = new URL('https://www.datasheets.com/api/v1/search');
  url.searchParams.set('q', query);
  url.searchParams.set('limit', String(limit));
  url.searchParams.set('page', String(page));

  const response = await fetch(url.toString(), {
    headers: {
      Authorization: `Bearer ${apiKey}`,
    },
  });

  if (!response.ok) {
    throw new Error(`API error: ${response.status}`);
  }

  return response.json() as Promise<SearchResponse>;
}
```

---

## Python (requests)

```python
import requests

def search_components(query: str, api_key: str, limit: int = 5, page: int = 1) -> dict:
    url = "https://www.datasheets.com/api/v1/search"
    headers = {"Authorization": f"Bearer {api_key}"}
    params = {"q": query, "limit": limit, "page": page}

    response = requests.get(url, headers=headers, params=params)
    response.raise_for_status()
    return response.json()

# Usage
data = search_components("bav99", "YOUR_API_KEY", limit=10)
for part in data["results"]:
    print(part)
```

---

## Python (httpx + async)

```python
import httpx
import asyncio

async def search_components(query: str, api_key: str, limit: int = 5, page: int = 1) -> dict:
    url = "https://www.datasheets.com/api/v1/search"
    headers = {"Authorization": f"Bearer {api_key}"}
    params = {"q": query, "limit": limit, "page": page}

    async with httpx.AsyncClient() as client:
        response = await client.get(url, headers=headers, params=params)
        response.raise_for_status()
        return response.json()

# Usage
data = asyncio.run(search_components("atmega328p", "YOUR_API_KEY"))
print(data["count"], "results found")
```

---

## Pagination Example

```js
async function getAllResults(query, apiKey, pageSize = 10) {
  let page = 1;
  let allResults = [];

  while (true) {
    const data = await searchComponents(query, apiKey, pageSize, page);
    allResults = allResults.concat(data.results);

    const totalPages = Math.ceil(data.count / pageSize);
    if (page >= totalPages) break;
    page++;
  }

  return allResults;
}
```
