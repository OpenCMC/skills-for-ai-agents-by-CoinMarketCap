# Official SDKs

Use the official SDKs when you write code for the user. They are generated from the CoinMarketCap OpenAPI spec, so they cover every endpoint in this skill. They send the API key, retry `429` and `5xx` responses with backoff, and return typed responses.

| Language | Install | Import |
|----------|---------|--------|
| JavaScript / TypeScript | `npm install @coinmarketcap/sdk` | `import { CoinMarketCap } from "@coinmarketcap/sdk"` |
| Python 3.10+ | `pip install coinmarketcap-sdk` | `from coinmarketcap import CoinMarketCap` |

## Create a client

```typescript
const cmc = new CoinMarketCap({ apiKey: process.env.CMC_PRO_API_KEY });
// Keyless endpoints work without a key:
const keyless = new CoinMarketCap({ environment: "public" });
```

```python
cmc = CoinMarketCap(api_key=os.environ["CMC_PRO_API_KEY"])
# Keyless endpoints work without a key:
keyless = CoinMarketCap(environment="public")
```

## Method for each endpoint

TypeScript methods hang off `cmc.api`, Python methods off the client: `cmc.api.cryptocurrency.quotesLatest(...)` and `cmc.cryptocurrency.quotes_latest(...)`. The Keyless column shows which endpoints also work with the keyless client.

| Endpoint | Summary | TypeScript (`cmc.api.`) | Python (`cmc.`) | Keyless |
|----------|---------|-------------------------|-----------------|---------|
| `GET /v1/cryptocurrency/categories` | Categories | `cryptocurrency.categories` | `cryptocurrency.categories` | Yes |
| `GET /v1/cryptocurrency/category` | Category | `cryptocurrency.category` | `cryptocurrency.category` | Yes |
| `GET /v1/cryptocurrency/listings/historical` | Listings Historical | `cryptocurrency.listingsHistorical` | `cryptocurrency.listings_historical` | No |
| `GET /v3/cryptocurrency/listings/latest` | Listings Latest | `cryptocurrency.listingsLatest` | `cryptocurrency.listings_latest` | Yes |
| `GET /v1/cryptocurrency/listings/new` | Listings New | `cryptocurrency.listingsNew` | `cryptocurrency.listings_new` | No |
| `GET /v1/cryptocurrency/map` | Cryptocurrency ID Map | `cryptocurrency.map` | `cryptocurrency.map` | Yes |
| `GET /v1/cryptocurrency/trending/gainers-losers` | Trending Gainers & Losers | `cryptocurrency.trendingGainerslosers` | `cryptocurrency.trending_gainerslosers` | No |
| `GET /v1/cryptocurrency/trending/latest` | Trending Latest | `cryptocurrency.trendingLatest` | `cryptocurrency.trending_latest` | No |
| `GET /v1/cryptocurrency/trending/most-visited` | Trending Most Visited | `cryptocurrency.trendingMostvisited` | `cryptocurrency.trending_mostvisited` | No |
| `GET /v2/cryptocurrency/info` | Metadata | `cryptocurrency.info` | `cryptocurrency.info` | Yes |
| `GET /v2/cryptocurrency/market-pairs/latest` | Market Pairs Latest | `cryptocurrency.marketpairsLatest` | `cryptocurrency.marketpairs_latest` | No |
| `GET /v2/cryptocurrency/ohlcv/historical` | OHLCV Historical | `cryptocurrency.ohlcvHistorical` | `cryptocurrency.ohlcv_historical` | No |
| `GET /v2/cryptocurrency/ohlcv/latest` | OHLCV Latest | `cryptocurrency.ohlcvLatest` | `cryptocurrency.ohlcv_latest` | No |
| `GET /v2/cryptocurrency/price-performance-stats/latest` | Price Performance Stats | `cryptocurrency.priceperformancestatsLatest` | `cryptocurrency.priceperformancestats_latest` | No |
| `GET /v3/cryptocurrency/quotes/latest` | Quotes Latest | `cryptocurrency.quotesLatest` | `cryptocurrency.quotes_latest` | Yes |
| `GET /v3/cryptocurrency/quotes/historical` | Quotes Historical | `cryptocurrency.quotesHistorical` | `cryptocurrency.quotes_historical` | No |

Two endpoints in this skill have newer versions:

- `GET /v1/cryptocurrency/listings/latest` is deprecated, so the table lists `GET /v3/cryptocurrency/listings/latest` in its place.
- `GET /v2/cryptocurrency/quotes/latest` is deprecated, so the table lists `GET /v3/cryptocurrency/quotes/latest` in its place.

## Parameters and results

TypeScript takes query parameters under `query`, with their API names: `{ query: { id: "1,1027", convert: "USD" } }`. It returns `{ data, error }`, where `data` is the parsed JSON body.

Python takes them as keyword arguments in snake_case: `id="1,1027"`, `convert="USD"`. API names in camelCase become snake_case (`tokenAddress` → `token_address`), and `from` becomes `from_`. It returns a typed model; call `.to_dict()` for a plain dict.

Pass values with the type the API reference gives. Some numeric-looking parameters are strings, for example `limit` on `/v3/cryptocurrency/listings/latest`.

## Errors

Both SDKs map HTTP errors to typed errors: `AuthenticationError` (401), `ForbiddenError` (403, for example an endpoint your plan does not include), `RateLimitError` (429) and `CMCError` for the rest, with the API's `status.error_message`. TypeScript returns them as `error`; Python raises them.

Full guides: [TypeScript SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/typescript), [Python SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/python).
