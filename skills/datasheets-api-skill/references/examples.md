# Datasheets.com API — Code Examples

These examples intentionally omit credential plumbing. Each snippet expects a
runtime-provided HTTP client or session that already injects Datasheets.com
authentication outside the prompt and code sample.

## cURL

```bash
# Request shape only. Execute it through a runtime/tool profile that injects auth.
curl -sS "https://www.datasheets.com/api/v1/search?q=bav99&limit=10&page=1"
```

---

## JavaScript (fetch)

```js
async function searchComponents(authenticatedFetch, query, limit = 5, page = 1) {
  const url = new URL('https://www.datasheets.com/api/v1/search');
  url.searchParams.set('q', query);
  url.searchParams.set('limit', String(limit));
  url.searchParams.set('page', String(page));

  const response = await authenticatedFetch(url.toString(), {
    method: 'GET',
  });

  if (response.status === 401) {
    throw new Error('Datasheets auth is not configured in this runtime');
  }

  if (!response.ok) {
    throw new Error(`API error: ${response.status}`);
  }

  const data = await response.json();
  return data; // { query, page, limit, count, results[] }
}

// Usage
const data = await searchComponents(runtime.authenticatedFetch, 'bav99', 10);
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

type AuthenticatedFetch = (
  input: RequestInfo | URL,
  init?: RequestInit
) => Promise<Response>;

async function searchComponents(
  authenticatedFetch: AuthenticatedFetch,
  query: string,
  limit = 5,
  page = 1
): Promise<SearchResponse> {
  const url = new URL('https://www.datasheets.com/api/v1/search');
  url.searchParams.set('q', query);
  url.searchParams.set('limit', String(limit));
  url.searchParams.set('page', String(page));

  const response = await authenticatedFetch(url.toString(), {
    method: 'GET',
  });

  if (response.status === 401) {
    throw new Error('Datasheets auth is not configured in this runtime');
  }

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

def search_components(
    session: requests.Session,
    query: str,
    limit: int = 5,
    page: int = 1,
) -> dict:
    url = "https://www.datasheets.com/api/v1/search"
    params = {"q": query, "limit": limit, "page": page}

    response = session.get(url, params=params)
    if response.status_code == 401:
        raise RuntimeError("Datasheets auth is not configured in this runtime")
    response.raise_for_status()
    return response.json()

# `authenticated_session` is preconfigured by the runtime to inject auth.
data = search_components(authenticated_session, "bav99", limit=10)
for part in data["results"]:
    print(part)
```

---

## Python (httpx + async)

```python
import asyncio
import httpx

async def search_components(
    client: httpx.AsyncClient,
    query: str,
    limit: int = 5,
    page: int = 1,
) -> dict:
    url = "https://www.datasheets.com/api/v1/search"
    params = {"q": query, "limit": limit, "page": page}

    response = await client.get(url, params=params)
    if response.status_code == 401:
        raise RuntimeError("Datasheets auth is not configured in this runtime")
    response.raise_for_status()
    return response.json()

# `authenticated_client` is preconfigured by the runtime to inject auth.
data = asyncio.run(search_components(authenticated_client, "atmega328p"))
print(data["count"], "results found")
```

---

## Pagination Example

```js
async function getAllResults(authenticatedFetch, query, pageSize = 10) {
  let page = 1;
  let allResults = [];

  while (true) {
    const data = await searchComponents(authenticatedFetch, query, pageSize, page);
    allResults = allResults.concat(data.results);

    const totalPages = Math.ceil(data.count / pageSize);
    if (page >= totalPages) break;
    page++;
  }

  return allResults;
}
```
