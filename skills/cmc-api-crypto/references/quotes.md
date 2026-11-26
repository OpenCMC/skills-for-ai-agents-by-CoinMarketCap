# Quotes API Reference

## Quotes Latest

**Path:** `GET /v3/cryptocurrency/quotes/latest`

**Description:** Returns the latest market quote for one or more cryptocurrencies. This is the primary endpoint for getting current prices, market cap, volume, and price changes.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | string | Conditional | One or more comma-separated CMC IDs |
| slug | string | Conditional | One or more comma-separated slugs |
| symbol | string | Conditional | One or more comma-separated symbols |
| convert | string | No | Currency to convert quotes (e.g., USD, EUR, BTC). Default: USD |
| convert_id | string | No | CMC ID of currency to convert to |
| aux | string | No | Additional fields: num_market_pairs, cmc_rank, date_added, tags, platform, max_supply, circulating_supply, total_supply, is_active, is_fiat. Default: all |
| skip_invalid | boolean | No | Skip invalid IDs instead of erroring. Default: false |

**Note:** At least one of `id`, `slug`, or `symbol` is required.

**Note:** `symbol` returns every cryptocurrency with that symbol, including inactive ones. `symbol=BTC,ETH` returns Bitcoin and Ethereum plus every other coin using BTC or ETH as its symbol. Use `id` when you know which coin you want.

### Response Fields

`data` is an array with one object per matched cryptocurrency, sorted by ID rather than in request order. Match results by `id`.

| Field | Description |
|-------|-------------|
| data[].id | CMC cryptocurrency ID |
| data[].name | Cryptocurrency name |
| data[].symbol | Cryptocurrency symbol |
| data[].slug | URL slug |
| data[].cmc_rank | Current CMC rank |
| data[].num_market_pairs | Number of trading pairs |
| data[].circulating_supply | Circulating supply |
| data[].total_supply | Total supply |
| data[].max_supply | Maximum supply |
| data[].infinite_supply | Has infinite supply |
| data[].last_updated | Last update timestamp |
| data[].date_added | Date added to CMC |
| data[].tags | Array of tag objects with `slug`, `name` and `category` |
| data[].platform | Platform details for tokens |
| data[].is_active | Whether actively tracked |
| data[].is_fiat | Whether it's a fiat currency |
| data[].quote[] | One object per `convert` currency |
| data[].quote[].id | CMC ID of the convert currency (2781 for USD) |
| data[].quote[].symbol | Symbol of the convert currency (e.g., USD) |
| data[].quote[].price | Current price |
| data[].quote[].volume_24h | 24h trading volume |
| data[].quote[].volume_change_24h | 24h volume change % |
| data[].quote[].cex_volume_24h | 24h volume on centralized exchanges |
| data[].quote[].dex_volume_24h | 24h volume on decentralized exchanges |
| data[].quote[].percent_change_1h | 1h price change % |
| data[].quote[].percent_change_24h | 24h price change % |
| data[].quote[].percent_change_7d | 7d price change % |
| data[].quote[].percent_change_30d | 30d price change % |
| data[].quote[].percent_change_60d | 60d price change % |
| data[].quote[].percent_change_90d | 90d price change % |
| data[].quote[].market_cap | Market cap |
| data[].quote[].market_cap_dominance | Market cap dominance % |
| data[].quote[].fully_diluted_market_cap | Fully diluted market cap |
| data[].quote[].last_updated | Quote last updated |

### Example

```bash
# Get quotes by ID
curl -X GET "https://pro-api.coinmarketcap.com/v3/cryptocurrency/quotes/latest?id=1,1027,825" \
  -H "X-CMC_PRO_API_KEY: your-api-key"

# Get quotes by symbol (returns every coin with these symbols)
curl -X GET "https://pro-api.coinmarketcap.com/v3/cryptocurrency/quotes/latest?symbol=BTC,ETH" \
  -H "X-CMC_PRO_API_KEY: your-api-key"

# Get quote in multiple currencies (one quote object per currency)
curl -X GET "https://pro-api.coinmarketcap.com/v3/cryptocurrency/quotes/latest?id=1&convert=USD,EUR,BTC" \
  -H "X-CMC_PRO_API_KEY: your-api-key"
```

### Example Response

Response for `id=1&convert=USD,EUR`, with `tags` shortened:

```json
{
  "status": {
    "timestamp": "2026-10-23T08:30:01.420Z",
    "error_code": "0",
    "error_message": "",
    "elapsed": 4,
    "credit_count": 2
  },
  "data": [
    {
      "id": 1,
      "name": "Bitcoin",
      "symbol": "BTC",
      "slug": "bitcoin",
      "cmc_rank": 1,
      "num_market_pairs": 12769,
      "circulating_supply": 20102287,
      "total_supply": 20102287,
      "max_supply": 21000000,
      "infinite_supply": false,
      "last_updated": "2026-10-23T08:29:00.000Z",
      "date_added": "2010-07-13T00:00:00.000Z",
      "tags": [
        { "slug": "mineable", "name": "Mineable", "category": "OTHERS" },
        { "slug": "pow", "name": "PoW", "category": "ALGORITHM" }
      ],
      "platform": null,
      "is_active": 1,
      "is_fiat": 0,
      "quote": [
        {
          "id": 2781,
          "symbol": "USD",
          "price": 81696.41087933285,
          "volume_24h": 27172704242.815926,
          "volume_change_24h": -28.1677,
          "cex_volume_24h": 27142341550.93963,
          "dex_volume_24h": 30362691.87629619,
          "percent_change_1h": 0.08363108,
          "percent_change_24h": -0.31197322,
          "percent_change_7d": -0.56646283,
          "percent_change_30d": -5.17341566,
          "percent_change_60d": 6.22956932,
          "percent_change_90d": 27.78018829,
          "market_cap": 1642284698366.2712,
          "market_cap_dominance": 59.9972,
          "fully_diluted_market_cap": 1715624628465.99,
          "last_updated": "2026-10-23T08:28:01.000Z"
        },
        {
          "id": 2790,
          "symbol": "EUR",
          "price": 72960.53196759461,
          "volume_24h": 24267099805.427277,
          "volume_change_24h": -28.1677,
          "cex_volume_24h": 24239983826.556004,
          "dex_volume_24h": 27115978.87127185,
          "percent_change_1h": 0.08363108,
          "percent_change_24h": -0.31197322,
          "percent_change_7d": -0.56646283,
          "percent_change_30d": -5.17341566,
          "percent_change_60d": 6.22956932,
          "percent_change_90d": 27.78018829,
          "market_cap": 1466673553285.2615,
          "market_cap_dominance": 59.9972,
          "fully_diluted_market_cap": 1532171171319.4868,
          "last_updated": "2026-10-23T08:29:05.000Z"
        }
      ]
    }
  ]
}
```

---

## Quotes Historical

**Path:** `GET /v3/cryptocurrency/quotes/historical`

**Description:** Returns historical market quotes for a cryptocurrency at specific intervals. Useful for charting and historical analysis.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | string | Conditional | CMC cryptocurrency ID |
| slug | string | Conditional | Cryptocurrency slug |
| symbol | string | Conditional | Cryptocurrency symbol |
| time_start | string | No | Start time (ISO 8601 or Unix timestamp) |
| time_end | string | No | End time (ISO 8601 or Unix timestamp) |
| count | integer | No | Number of intervals. Default: 10, Max: 10000 |
| interval | string | No | Interval: hourly, daily, weekly, monthly, yearly, 5m, 10m, 15m, 30m, 45m, 1h, 2h, 3h, 4h, 6h, 12h, 24h, 1d, 2d, 3d, 7d, 14d, 15d, 30d, 60d, 90d, 365d. Default: 5m |
| convert | string | No | Currency to convert quotes |
| convert_id | string | No | CMC ID of currency to convert to |
| aux | string | No | Additional fields: price, volume, market_cap, circulating_supply, total_supply, quote_timestamp, is_active, is_fiat, search_interval. Default: all |
| skip_invalid | boolean | No | Skip invalid values instead of erroring |

**Note:** At least one of `id`, `slug`, or `symbol` is required.

### Response Fields

Unlike Quotes Latest, `data` here is an object keyed by CMC ID (`data["1"]`), and each `quote` is an object keyed by currency symbol. A `symbol` lookup is keyed by symbol instead, with an array of every matching coin as its value.

| Field | Description |
|-------|-------------|
| data[id].id | CMC cryptocurrency ID |
| data[id].name | Cryptocurrency name |
| data[id].symbol | Cryptocurrency symbol |
| data[id].is_active | Whether actively tracked |
| data[id].is_fiat | Whether it's fiat |
| data[id].quotes[].timestamp | Quote timestamp |
| data[id].quotes[].quote.USD.price | Price at timestamp |
| data[id].quotes[].quote.USD.volume_24h | 24h volume at timestamp |
| data[id].quotes[].quote.USD.market_cap | Market cap at timestamp |
| data[id].quotes[].quote.USD.circulating_supply | Circulating supply at timestamp |
| data[id].quotes[].quote.USD.total_supply | Total supply at timestamp |
| data[id].quotes[].quote.USD.timestamp | Quote data timestamp |

### Example

```bash
# Get daily quotes for last 30 days
curl -X GET "https://pro-api.coinmarketcap.com/v3/cryptocurrency/quotes/historical?id=1&interval=daily&count=30" \
  -H "X-CMC_PRO_API_KEY: your-api-key"

# Get hourly quotes for specific date range
curl -X GET "https://pro-api.coinmarketcap.com/v3/cryptocurrency/quotes/historical?id=1&time_start=2024-01-01&time_end=2024-01-07&interval=hourly" \
  -H "X-CMC_PRO_API_KEY: your-api-key"

# Get 5-minute data for recent hours
curl -X GET "https://pro-api.coinmarketcap.com/v3/cryptocurrency/quotes/historical?symbol=BTC&interval=5m&count=100" \
  -H "X-CMC_PRO_API_KEY: your-api-key"
```

### Example Response

```json
{
  "status": {
    "timestamp": "2024-01-15T12:00:00.000Z",
    "error_code": 0,
    "error_message": null,
    "credit_count": 1
  },
  "data": {
    "1": {
      "id": 1,
      "name": "Bitcoin",
      "symbol": "BTC",
      "is_active": 1,
      "is_fiat": 0,
      "quotes": [
        {
          "timestamp": "2024-01-01T00:00:00.000Z",
          "quote": {
            "USD": {
              "price": 42345.67,
              "volume_24h": 21234567890,
              "market_cap": 829876543210,
              "circulating_supply": 19590000,
              "total_supply": 19590000,
              "timestamp": "2024-01-01T00:00:00.000Z"
            }
          }
        },
        {
          "timestamp": "2024-01-02T00:00:00.000Z",
          "quote": {
            "USD": {
              "price": 43123.45,
              "volume_24h": 22345678901,
              "market_cap": 845123456789,
              "circulating_supply": 19591000,
              "total_supply": 19591000,
              "timestamp": "2024-01-02T00:00:00.000Z"
            }
          }
        }
      ]
    }
  }
}
```

### Important Notes

1. **Historical data availability**: Historical data availability depends on your API plan. Free tier has limited historical access.

2. **Interval selection**: Choose intervals appropriate for your time range. Using 5m intervals over months will return huge datasets.

3. **Time parameters**: Use ISO 8601 format (2024-01-15T00:00:00Z) or Unix timestamps (1705276800).

4. **Credit usage**: Historical queries consume more credits than latest quotes. Larger time ranges and shorter intervals use more credits.

5. **Data gaps**: Some intervals may have missing data points due to API downtime or data quality issues.
