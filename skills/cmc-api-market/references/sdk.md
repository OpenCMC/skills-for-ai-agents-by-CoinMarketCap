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
| `GET /v1/global-metrics/quotes/historical` | Quotes Historical | `globalMetrics.quotesHistorical` | `global_metrics.globalmetrics_quotes_historical` | `GlobalMetrics.QuotesHistorical` | No |
| `GET /v1/global-metrics/quotes/latest` | Quotes Latest | `globalMetrics.quotesLatest` | `global_metrics.globalmetrics_quotes_latest` | `GlobalMetrics.QuotesLatest` | Yes |
| `GET /v3/fear-and-greed/historical` | CMC Crypto Fear and Greed Historical | `globalMetrics.fearandgreedHistorical` | `global_metrics.fearandgreed_historical` | `GlobalMetrics.FearandgreedHistorical` | Yes |
| `GET /v3/fear-and-greed/latest` | CMC Crypto Fear and Greed Latest | `globalMetrics.fearandgreedLatest` | `global_metrics.fearandgreed_latest` | `GlobalMetrics.FearandgreedLatest` | Yes |
| `GET /v3/index/cmc100-historical` | CoinMarketCap 100 Index Historical | `cmcIndex.indexCMC100Historical` | `cmc_index.index_cmc100_historical` | `CmcIndex.IndexCMC100Historical` | Yes |
| `GET /v3/index/cmc100-latest` | CoinMarketCap 100 Index Latest | `cmcIndex.indexCMC100Latest` | `cmc_index.index_cmc100_latest` | `CmcIndex.IndexCMC100Latest` | Yes |
| `GET /v3/index/cmc20-historical` | CoinMarketCap 20 Index Historical | `cmcIndex.indexCMC20Historical` | `cmc_index.index_cmc20_historical` | `CmcIndex.IndexCMC20Historical` | Yes |
| `GET /v3/index/cmc20-latest` | CoinMarketCap 20 Index Latest | `cmcIndex.indexCMC20Latest` | `cmc_index.index_cmc20_latest` | `CmcIndex.IndexCMC20Latest` | Yes |
| `GET /v1/community/trending/token` | Community Trending Tokens | `community.trendingToken` | `community.trending_token` | `Community.TrendingToken` | No |
| `GET /v1/community/trending/topic` | Community Trending Topics | `community.trendingTopic` | `community.trending_topic` | `Community.TrendingTopic` | No |
| `GET /v1/content/latest` | Content Latest | `content.latest` | `content.latest` | `Content.Latest` | No |
| `GET /v1/content/posts/comments` | Content Post Comments | `content.postsComments` | `content.posts_comments` | `Content.PostsComments` | No |
| `GET /v1/content/posts/latest` | Content Latest Posts | `content.postsLatest` | `content.posts_latest` | `Content.PostsLatest` | No |
| `GET /v1/content/posts/top` | Content Top Posts | `content.postsTop` | `content.posts_top` | `Content.PostsTop` | No |
| `GET /v1/k-line/candles` | Get K-line candles | `ohlcv.klineCandles` | `ohlcv.kline_candles` | `Ohlcv.KlineCandles` | Yes |
| `GET /v1/k-line/points` | Get K-line points | `ohlcv.klinePoints` | `ohlcv.kline_points` | `Ohlcv.KlinePoints` | Yes |
| `GET /v1/fiat/map` | Fiat ID Map | `tools.fiatMap` | `tools.fiat_map` | `Tools.FiatMap` | No |
| `GET /v1/key/info` | Key Info | `tools.keyInfo` | `tools.key_info` | `Tools.KeyInfo` | No |
| `GET /v2/tools/price-conversion` | Price Conversion v2 | `tools.priceconversion` | `tools.priceconversion` | `Tools.Priceconversion` | Yes |

## Parameters and results

TypeScript takes query parameters under `query`, with their API names: `{ query: { id: "1,1027", convert: "USD" } }`. It returns `{ data, error }`, where `data` is the parsed JSON body.

Python takes them as keyword arguments in snake_case: `id="1,1027"`, `convert="USD"`. API names in camelCase become snake_case (`tokenAddress` → `token_address`), and `from` becomes `from_`. It returns a typed model; call `.to_dict()` for a plain dict.

Go takes a params struct named after the method, with Go field names (`id` → `ID`, `tokenAddress` → `TokenAddress`): `&coinmarketcap.CryptocurrencyQuotesLatestParams{ID: coinmarketcap.String("1,1027")}`. Required fields are plain values. Optional fields are pointers; set them with `coinmarketcap.String`, `Int`, `Float64`, `Bool` or `Ptr`. Enum parameters have their own string types, for example `coinmarketcap.Ptr(coinmarketcap.GetV3CryptocurrencyQuotesHistoricalParamsInterval("1d"))`. Pass `nil` to send no parameters; methods without any parameters take only the context. It returns a typed response struct and an `error`.

Pass values with the type the API reference gives. Some numeric-looking parameters are strings, for example `limit` on `/v3/cryptocurrency/listings/latest`.

## Errors

All three SDKs map HTTP errors to typed errors: `AuthenticationError` (401), `ForbiddenError` (403, for example an endpoint your plan does not include), `RateLimitError` (429) and `CMCError` for the rest, with the API's `status.error_message`. TypeScript returns them as `error` and Python raises them. Go returns them as the `error` value; check the type with `errors.As`, for example `var rateLimited *coinmarketcap.RateLimitError`.

Full guides: [TypeScript SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/typescript), [Python SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/python), [Go SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/go).
