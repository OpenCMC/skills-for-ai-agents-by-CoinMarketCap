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
| `GET /v1/dex/token` | Get token detail | `token.token` | `token.token` | Yes |
| `GET /v1/dex/token/price` | Get token price | `token.price` | `token.price` | Yes |
| `POST /v1/dex/token/price/batch` | Batch get token prices | `token.batchGetTokenPrice` | `token.batch_get_token_price` | No |
| `GET /v1/dex/token/pools` | Get token pools | `token.pools` | `token.pools` | Yes |
| `GET /v1/dex/token-liquidity/query` | Query token liquidity | `token.queryTokenLiquidity` | `token.query_token_liquidity` | Yes |
| `POST /v1/dex/tokens/batch-query` | Batch query tokens | `token.batchQueryTokens` | `token.batch_query_tokens` | No |
| `GET /v1/dex/tokens/transactions` | Get swap list | `token.swapList` | `token.swap_list` | Yes |
| `POST /v1/dex/tokens/trending/list` | Get trending tokens | `token.trendingList` | `token.trending_list` | No |
| `GET /v4/dex/pairs/quotes/latest` | Quotes Latest | `token.latestPairsQuotes` | `token.latest_pairs_quotes` | Yes |
| `GET /v4/dex/spot-pairs/latest` | Pairs Listings Latest | `token.spotPairsLatest` | `token.spot_pairs_latest` | Yes |
| `GET /v1/dex/platform/list` | Get platform list | `platform.list` | `platform.list` | Yes |
| `GET /v1/dex/platform/detail` | Get platform detail | `platform.detail` | `platform.detail` | Yes |
| `GET /v1/dex/search` | Search tokens | `token.search` | `token.search` | Yes |
| `POST /v1/dex/gainer-loser/list` | Get top gainers and losers | `token.gainerLoserList` | `token.gainer_loser_list` | No |
| `GET /v1/dex/liquidity-change/list` | Get liquidity change list | `token.liquidityChangeList` | `token.liquidity_change_list` | Yes |
| `POST /v1/dex/meme/list` | Get meme tokens | `token.memeList` | `token.meme_list` | No |
| `POST /v1/dex/new/list` | Get new tokens | `token.newList` | `token.new_list` | No |
| `GET /v1/dex/security/detail` | Get security detail | `token.securityDetail` | `token.security_detail` | Yes |

## Parameters and results

TypeScript takes query parameters under `query`, with their API names: `{ query: { id: "1,1027", convert: "USD" } }`. It returns `{ data, error }`, where `data` is the parsed JSON body.

Python takes them as keyword arguments in snake_case: `id="1,1027"`, `convert="USD"`. API names in camelCase become snake_case (`tokenAddress` → `token_address`), and `from` becomes `from_`. It returns a typed model; call `.to_dict()` for a plain dict.

Pass values with the type the API reference gives. Some numeric-looking parameters are strings, for example `limit` on `/v3/cryptocurrency/listings/latest`.

## POST endpoints

POST endpoints take a JSON body. TypeScript passes it as `body`; Python wraps it in the request model named in the API reference:

```typescript
const { data, error } = await cmc.api.token.batchGetTokenPrice({
  body: {
    tokens: [
      {
        platform: "ethereum",
        address: "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
      },
    ],
  },
});
```

```python
from coinmarketcap.models import DqueryBatchPriceRequestDTO

body = DqueryBatchPriceRequestDTO.from_dict({
    "tokens": [
        {
            "platform": "ethereum",
            "address": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
        },
    ],
})
result = cmc.token.batch_get_token_price(body=body)
```

## Errors

Both SDKs map HTTP errors to typed errors: `AuthenticationError` (401), `ForbiddenError` (403, for example an endpoint your plan does not include), `RateLimitError` (429) and `CMCError` for the rest, with the API's `status.error_message`. TypeScript returns them as `error`; Python raises them.

Full guides: [TypeScript SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/typescript), [Python SDK](https://coinmarketcap.com/api/documentation/developer-tools/sdks/python).
