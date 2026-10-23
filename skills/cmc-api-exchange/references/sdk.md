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
| `GET /v1/exchange/map` | Exchange ID Map | `exchange.map` | `exchange.map` | Yes |
| `GET /v1/exchange/info` | Metadata | `exchange.info` | `exchange.info` | No |
| `GET /v1/exchange/listings/latest` | Listings Latest | `exchange.listingsLatest` | `exchange.listings_latest` | No |
| `GET /v1/exchange/quotes/latest` | Quotes Latest | `exchange.quotesLatest` | `exchange.quotes_latest` | No |
| `GET /v1/exchange/quotes/historical` | Quotes Historical | `exchange.quotesHistorical` | `exchange.quotes_historical` | No |
| `GET /v1/exchange/market-pairs/latest` | Market Pairs Latest | `exchange.marketpairsLatest` | `exchange.marketpairs_latest` | No |
| `GET /v1/exchange/assets` | Exchange Assets | `exchange.assets` | `exchange.assets` | No |

## Parameters and results

TypeScript takes query parameters under `query`, with their API names: `{ query: { id: "1,1027", convert: "USD" } }`. It returns `{ data, error }`, where `data` is the parsed JSON body.

Python takes them as keyword arguments in snake_case: `id="1,1027"`, `convert="USD"`. API names in camelCase become snake_case (`tokenAddress` → `token_address`), and `from` becomes `from_`. It returns a typed model; call `.to_dict()` for a plain dict.

Pass values with the type the API reference gives. Some numeric-looking parameters are strings, for example `limit` on `/v3/cryptocurrency/listings/latest`.

## Errors

Both SDKs map HTTP errors to typed errors: `AuthenticationError` (401), `ForbiddenError` (403, for example an endpoint your plan does not include), `RateLimitError` (429) and `CMCError` for the rest, with the API's `status.error_message`. TypeScript returns them as `error`; Python raises them.

Full guides: [TypeScript SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/typescript), [Python SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/python).
