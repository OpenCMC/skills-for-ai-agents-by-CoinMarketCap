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
| `GET /v1/global-metrics/quotes/historical` | Quotes Historical | `globalMetrics.quotesHistorical` | `global_metrics.globalmetrics_quotes_historical` | No |
| `GET /v1/global-metrics/quotes/latest` | Quotes Latest | `globalMetrics.quotesLatest` | `global_metrics.globalmetrics_quotes_latest` | Yes |
| `GET /v3/fear-and-greed/historical` | CMC Crypto Fear and Greed Historical | `globalMetrics.fearandgreedHistorical` | `global_metrics.fearandgreed_historical` | Yes |
| `GET /v3/fear-and-greed/latest` | CMC Crypto Fear and Greed Latest | `globalMetrics.fearandgreedLatest` | `global_metrics.fearandgreed_latest` | Yes |
| `GET /v3/index/cmc100-historical` | CoinMarketCap 100 Index Historical | `cmcIndex.indexCMC100Historical` | `cmc_index.index_cmc100_historical` | Yes |
| `GET /v3/index/cmc100-latest` | CoinMarketCap 100 Index Latest | `cmcIndex.indexCMC100Latest` | `cmc_index.index_cmc100_latest` | Yes |
| `GET /v3/index/cmc20-historical` | CoinMarketCap 20 Index Historical | `cmcIndex.indexCMC20Historical` | `cmc_index.index_cmc20_historical` | Yes |
| `GET /v3/index/cmc20-latest` | CoinMarketCap 20 Index Latest | `cmcIndex.indexCMC20Latest` | `cmc_index.index_cmc20_latest` | Yes |
| `GET /v1/community/trending/token` | Community Trending Tokens | `community.trendingToken` | `community.trending_token` | No |
| `GET /v1/community/trending/topic` | Community Trending Topics | `community.trendingTopic` | `community.trending_topic` | No |
| `GET /v1/content/latest` | Content Latest | `content.latest` | `content.latest` | No |
| `GET /v1/content/posts/comments` | Content Post Comments | `content.postsComments` | `content.posts_comments` | No |
| `GET /v1/content/posts/latest` | Content Latest Posts | `content.postsLatest` | `content.posts_latest` | No |
| `GET /v1/content/posts/top` | Content Top Posts | `content.postsTop` | `content.posts_top` | No |
| `GET /v1/k-line/candles` | Get K-line candles | `ohlcv.klineCandles` | `ohlcv.kline_candles` | Yes |
| `GET /v1/k-line/points` | Get K-line points | `ohlcv.klinePoints` | `ohlcv.kline_points` | Yes |
| `GET /v1/fiat/map` | Fiat ID Map | `tools.fiatMap` | `tools.fiat_map` | No |
| `GET /v1/key/info` | Key Info | `tools.keyInfo` | `tools.key_info` | No |
| `GET /v2/tools/price-conversion` | Price Conversion v2 | `tools.priceconversion` | `tools.priceconversion` | Yes |

## Parameters and results

TypeScript takes query parameters under `query`, with their API names: `{ query: { id: "1,1027", convert: "USD" } }`. It returns `{ data, error }`, where `data` is the parsed JSON body.

Python takes them as keyword arguments in snake_case: `id="1,1027"`, `convert="USD"`. API names in camelCase become snake_case (`tokenAddress` → `token_address`), and `from` becomes `from_`. It returns a typed model; call `.to_dict()` for a plain dict.

Pass values with the type the API reference gives. Some numeric-looking parameters are strings, for example `limit` on `/v3/cryptocurrency/listings/latest`.

## Errors

Both SDKs map HTTP errors to typed errors: `AuthenticationError` (401), `ForbiddenError` (403, for example an endpoint your plan does not include), `RateLimitError` (429) and `CMCError` for the rest, with the API's `status.error_message`. TypeScript returns them as `error`; Python raises them.

Full guides: [TypeScript SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/typescript), [Python SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/python).
