# Official SDKs

Use the official SDKs when you write code for the user. They are generated from the CoinMarketCap OpenAPI spec, so they cover every endpoint in this skill. They send the API key, retry `429` and `5xx` responses with backoff, and return typed responses.

| Language | Install | Import |
|----------|---------|--------|
| JavaScript / TypeScript | `npm install @coinmarketcap/sdk` | `import { CoinMarketCap } from "@coinmarketcap/sdk"` |
| Python 3.10+ | `pip install coinmarketcap-sdk` | `from coinmarketcap import CoinMarketCap` |
| Go 1.24+ | `go get github.com/OpenCMC/coinmarketcap-api-go/v5` | `import "github.com/OpenCMC/coinmarketcap-api-go/v5"` (package `coinmarketcap`) |

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

```go
cmc, err := coinmarketcap.New(coinmarketcap.WithAPIKey(os.Getenv("CMC_PRO_API_KEY")))
// Keyless endpoints work without a key:
keyless, err := coinmarketcap.New(coinmarketcap.WithEnvironment(coinmarketcap.EnvironmentPublic))
```

## Method for each endpoint

TypeScript methods hang off `cmc.api`, Python and Go methods off the client: `cmc.api.cryptocurrency.quotesLatest(...)`, `cmc.cryptocurrency.quotes_latest(...)` and `cmc.Cryptocurrency.QuotesLatest(ctx, ...)`. The Keyless column shows which endpoints also work with the keyless client.

| Endpoint | Summary | TypeScript (`cmc.api.`) | Python (`cmc.`) | Go (`cmc.`) | Keyless |
|----------|---------|-------------------------|-----------------|-------------|---------|
| `GET /v1/cryptocurrency/categories` | Categories | `cryptocurrency.categories` | `cryptocurrency.categories` | `Cryptocurrency.Categories` | Yes |
| `GET /v1/cryptocurrency/category` | Category | `cryptocurrency.category` | `cryptocurrency.category` | `Cryptocurrency.Category` | Yes |
| `GET /v1/cryptocurrency/listings/historical` | Listings Historical | `cryptocurrency.listingsHistorical` | `cryptocurrency.listings_historical` | `Cryptocurrency.ListingsHistorical` | No |
| `GET /v3/cryptocurrency/listings/latest` | Listings Latest | `cryptocurrency.listingsLatest` | `cryptocurrency.listings_latest` | `Cryptocurrency.ListingsLatest` | Yes |
| `GET /v1/cryptocurrency/listings/new` | Listings New | `cryptocurrency.listingsNew` | `cryptocurrency.listings_new` | `Cryptocurrency.ListingsNew` | No |
| `GET /v1/cryptocurrency/map` | Cryptocurrency ID Map | `cryptocurrency.map` | `cryptocurrency.map` | `Cryptocurrency.Map` | Yes |
| `GET /v1/cryptocurrency/trending/gainers-losers` | Trending Gainers & Losers | `cryptocurrency.trendingGainerslosers` | `cryptocurrency.trending_gainerslosers` | `Cryptocurrency.TrendingGainerslosers` | No |
| `GET /v1/cryptocurrency/trending/latest` | Trending Latest | `cryptocurrency.trendingLatest` | `cryptocurrency.trending_latest` | `Cryptocurrency.TrendingLatest` | No |
| `GET /v1/cryptocurrency/trending/most-visited` | Trending Most Visited | `cryptocurrency.trendingMostvisited` | `cryptocurrency.trending_mostvisited` | `Cryptocurrency.TrendingMostvisited` | No |
| `GET /v2/cryptocurrency/info` | Metadata | `cryptocurrency.info` | `cryptocurrency.info` | `Cryptocurrency.Info` | Yes |
| `GET /v2/cryptocurrency/market-pairs/latest` | Market Pairs Latest | `cryptocurrency.marketpairsLatest` | `cryptocurrency.marketpairs_latest` | `Cryptocurrency.MarketpairsLatest` | No |
| `GET /v2/cryptocurrency/ohlcv/historical` | OHLCV Historical | `cryptocurrency.ohlcvHistorical` | `cryptocurrency.ohlcv_historical` | `Cryptocurrency.OhlcvHistorical` | No |
| `GET /v2/cryptocurrency/ohlcv/latest` | OHLCV Latest | `cryptocurrency.ohlcvLatest` | `cryptocurrency.ohlcv_latest` | `Cryptocurrency.OhlcvLatest` | No |
| `GET /v2/cryptocurrency/price-performance-stats/latest` | Price Performance Stats | `cryptocurrency.priceperformancestatsLatest` | `cryptocurrency.priceperformancestats_latest` | `Cryptocurrency.PriceperformancestatsLatest` | No |
| `GET /v3/cryptocurrency/quotes/latest` | Quotes Latest | `cryptocurrency.quotesLatest` | `cryptocurrency.quotes_latest` | `Cryptocurrency.QuotesLatest` | Yes |
| `GET /v3/cryptocurrency/quotes/historical` | Quotes Historical | `cryptocurrency.quotesHistorical` | `cryptocurrency.quotes_historical` | `Cryptocurrency.QuotesHistorical` | No |

Two endpoints in this skill have newer versions:

- `GET /v1/cryptocurrency/listings/latest` is deprecated, so the table lists `GET /v3/cryptocurrency/listings/latest` in its place.
- `GET /v2/cryptocurrency/quotes/latest` is deprecated, so the table lists `GET /v3/cryptocurrency/quotes/latest` in its place.

## Parameters and results

TypeScript takes query parameters under `query`, with their API names: `{ query: { id: "1,1027", convert: "USD" } }`. It returns `{ data, error }`, where `data` is the parsed JSON body.

Python takes them as keyword arguments in snake_case: `id="1,1027"`, `convert="USD"`. API names in camelCase become snake_case (`tokenAddress` → `token_address`), and `from` becomes `from_`. It returns a typed model; call `.to_dict()` for a plain dict.

Go takes a params struct named after the method, with Go field names (`id` → `ID`, `tokenAddress` → `TokenAddress`): `&coinmarketcap.CryptocurrencyQuotesLatestParams{ID: coinmarketcap.String("1,1027")}`. Required fields are plain values. Optional fields are pointers; set them with `coinmarketcap.String`, `Int`, `Float64`, `Bool` or `Ptr`. Enum parameters have their own string types, for example `coinmarketcap.Ptr(coinmarketcap.GetV3CryptocurrencyQuotesHistoricalParamsInterval("1d"))`. Pass `nil` to send no parameters; methods without any parameters take only the context. It returns a typed response struct and an `error`.

Pass values with the type the API reference gives. Some numeric-looking parameters are strings, for example `limit` on `/v3/cryptocurrency/listings/latest`.

## Errors

All three SDKs map HTTP errors to typed errors: `AuthenticationError` (401), `ForbiddenError` (403, for example an endpoint your plan does not include), `RateLimitError` (429) and `CMCError` for the rest, with the API's `status.error_message`. TypeScript returns them as `error` and Python raises them. Go returns them as the `error` value; check the type with `errors.As`, for example `var rateLimited *coinmarketcap.RateLimitError`.

Full guides: [TypeScript SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/typescript), [Python SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/python), [Go SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/go).
