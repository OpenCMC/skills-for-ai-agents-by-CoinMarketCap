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
| `GET /v1/exchange/map` | Exchange ID Map | `exchange.map` | `exchange.map` | `Exchange.Map` | Yes |
| `GET /v1/exchange/info` | Metadata | `exchange.info` | `exchange.info` | `Exchange.Info` | No |
| `GET /v1/exchange/listings/latest` | Listings Latest | `exchange.listingsLatest` | `exchange.listings_latest` | `Exchange.ListingsLatest` | No |
| `GET /v1/exchange/quotes/latest` | Quotes Latest | `exchange.quotesLatest` | `exchange.quotes_latest` | `Exchange.QuotesLatest` | No |
| `GET /v1/exchange/quotes/historical` | Quotes Historical | `exchange.quotesHistorical` | `exchange.quotes_historical` | `Exchange.QuotesHistorical` | No |
| `GET /v1/exchange/market-pairs/latest` | Market Pairs Latest | `exchange.marketpairsLatest` | `exchange.marketpairs_latest` | `Exchange.MarketpairsLatest` | No |
| `GET /v1/exchange/assets` | Exchange Assets | `exchange.assets` | `exchange.assets` | `Exchange.Assets` | No |

## Parameters and results

TypeScript takes query parameters under `query`, with their API names: `{ query: { id: "1,1027", convert: "USD" } }`. It returns `{ data, error }`, where `data` is the parsed JSON body.

Python takes them as keyword arguments in snake_case: `id="1,1027"`, `convert="USD"`. API names in camelCase become snake_case (`tokenAddress` → `token_address`), and `from` becomes `from_`. It returns a typed model; call `.to_dict()` for a plain dict.

Go takes a params struct named after the method, with Go field names (`id` → `ID`, `tokenAddress` → `TokenAddress`): `&coinmarketcap.CryptocurrencyQuotesLatestParams{ID: coinmarketcap.String("1,1027")}`. Required fields are plain values. Optional fields are pointers; set them with `coinmarketcap.String`, `Int`, `Float64`, `Bool` or `Ptr`. Enum parameters have their own string types, for example `coinmarketcap.Ptr(coinmarketcap.GetV3CryptocurrencyQuotesHistoricalParamsInterval("1d"))`. Pass `nil` to send no parameters; methods without any parameters take only the context. It returns a typed response struct and an `error`.

Pass values with the type the API reference gives. Some numeric-looking parameters are strings, for example `limit` on `/v3/cryptocurrency/listings/latest`.

## Errors

All three SDKs map HTTP errors to typed errors: `AuthenticationError` (401), `ForbiddenError` (403, for example an endpoint your plan does not include), `RateLimitError` (429) and `CMCError` for the rest, with the API's `status.error_message`. TypeScript returns them as `error` and Python raises them. Go returns them as the `error` value; check the type with `errors.As`, for example `var rateLimited *coinmarketcap.RateLimitError`.

Full guides: [TypeScript SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/typescript), [Python SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/python), [Go SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/go).
