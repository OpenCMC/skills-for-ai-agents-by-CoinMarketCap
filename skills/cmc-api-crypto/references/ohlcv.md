# OHLCV API Reference

## OHLCV Latest

**Path:** `GET /v2/cryptocurrency/ohlcv/latest`

**Description:** Returns the latest OHLCV (Open, High, Low, Close, Volume) data for one or more cryptocurrencies. Returns the last completed UTC day by default.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | string | Conditional | One or more comma-separated CMC IDs |
| slug | string | Conditional | One or more comma-separated slugs |
| symbol | string | Conditional | One or more comma-separated symbols |
| convert | string | No | Currency to convert (e.g., USD, EUR, BTC). Default: USD |
| convert_id | string | No | CMC ID of currency to convert to |
| skip_invalid | boolean | No | Skip invalid IDs instead of erroring. Default: false |

**Note:** At least one of `id`, `slug`, or `symbol` is required.

### Response Fields

| Field | Description |
|-------|-------------|
| data[id].id | CMC cryptocurrency ID |
| data[id].name | Cryptocurrency name |
| data[id].symbol | Cryptocurrency symbol |
| data[id].last_updated | Last update timestamp |
| data[id].time_open | Period open time |
| data[id].time_close | Period close time |
| data[id].time_high | Time of period high |
| data[id].time_low | Time of period low |
| data[id].quote.USD.open | Opening price |
| data[id].quote.USD.high | Highest price in period |
| data[id].quote.USD.low | Lowest price in period |
| data[id].quote.USD.close | Closing price |
| data[id].quote.USD.volume | Total volume in period |
| data[id].quote.USD.market_cap | Market cap at close |
| data[id].quote.USD.timestamp | Quote timestamp |

### Example

```bash
# Get latest OHLCV by ID
curl -X GET "https://pro-api.coinmarketcap.com/v2/cryptocurrency/ohlcv/latest?id=1,1027" \
  -H "X-CMC_PRO_API_KEY: your-api-key"

# Get latest OHLCV by symbol
curl -X GET "https://pro-api.coinmarketcap.com/v2/cryptocurrency/ohlcv/latest?symbol=BTC,ETH&convert=USD" \
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
      "last_updated": "2024-01-15T00:00:00.000Z",
      "time_open": "2024-01-14T00:00:00.000Z",
      "time_close": "2024-01-14T23:59:59.999Z",
      "time_high": "2024-01-14T14:35:00.000Z",
      "time_low": "2024-01-14T03:20:00.000Z",
      "quote": {
        "USD": {
          "open": 42100.00,
          "high": 43200.00,
          "low": 41800.00,
          "close": 42567.89,
          "volume": 23456789012,
          "market_cap": 834567890123,
          "timestamp": "2024-01-15T00:00:00.000Z"
        }
      }
    }
  }
}
```

---

## OHLCV Historical

**Path:** `GET /v5/cryptocurrency/ohlcv/historical`

**Description:** Returns historical OHLCV candles for one cryptocurrency, with the market cap and the times of the high and the low in each candle. Each candle is a compact row of 10 values, oldest first, so long histories stay small and load straight into charting and analysis tools.

This is the current OHLCV Historical endpoint. The older `GET /v2/cryptocurrency/ohlcv/historical` is deprecated: use v5 for new code.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | string | Conditional | One CMC cryptocurrency ID, e.g. `1` |
| slug | string | Conditional | One cryptocurrency slug, e.g. `bitcoin` |
| interval | string | Yes | Candle period: `15m`, `1h`, `4h`, `1d`, `1w` (weeks start Monday), `1M` (calendar month). Case-sensitive: `1m` is not accepted |
| time_start | string | No | Start of the window, inclusive (Unix timestamp or ISO 8601), rounded down to the start of its period |
| time_end | string | No | End of the window, inclusive (Unix timestamp or ISO 8601) |
| count | integer | No | Number of candles, 1 to 10000. Defaults to 10 unless you send both `time_start` and `time_end` |
| convert_id | string | No | One CMC currency ID for prices, volume and market cap. Default: `2781` (USD) |

**Note:** Send exactly one of `id` or `slug`. v5 rejects parameters it doesn't support (`symbol`, `convert`, `time_period`, `skip_invalid`) with a `400` error. To look up an ID from a symbol, use `/v1/cryptocurrency/map` first. For several currencies, make one request per `convert_id`.

### Time Window

`time_start` and `time_end` are inclusive and rounded down to the start of their period in UTC.

| You send | You get |
|----------|---------|
| `time_start` and `time_end` | Every period in the range, up to 10000 |
| `time_start` and `count` | `count` periods starting at `time_start` |
| `time_end` and `count` | `count` periods ending with the period that contains `time_end` |
| `time_start`, `time_end` and `count` | `count` periods starting at `time_start` (`time_end` doesn't change the window) |
| Neither `time_start` nor `time_end` | The `count` most recent completed periods |

To get a full date range, send `time_start` and `time_end` without `count`.

### Response Fields

`data` is an array of candles, oldest first. Each candle is an array of 10 values in a fixed order, so read them by position:

| Index | Field | Description |
|-------|-------|-------------|
| 0 | openPrice | Open price |
| 1 | highPrice | Highest price in the period |
| 2 | lowPrice | Lowest price in the period |
| 3 | closePrice | Close price |
| 4 | volume | Trading volume in the period |
| 5 | marketcap | Market cap at the close |
| 6 | openTime | Start of the period (ISO 8601, UTC) |
| 7 | highTime | Time of the high |
| 8 | lowTime | Time of the low |
| 9 | closeTime | End of the period, e.g. `2025-01-02T23:59:59.999Z` |

A value that isn't available is `null` in its position. Periods with no data, future periods and the period that is still open are left out, so a window with no data returns an empty `data` array.

### Example

```bash
# Last 30 daily candles
curl -X GET "https://pro-api.coinmarketcap.com/v5/cryptocurrency/ohlcv/historical?id=1&interval=1d&count=30" \
  -H "X-CMC_PRO_API_KEY: your-api-key"

# Hourly candles for the last 7 days (intraday history depends on your plan)
curl -X GET "https://pro-api.coinmarketcap.com/v5/cryptocurrency/ohlcv/historical?id=1&interval=1h&count=168" \
  -H "X-CMC_PRO_API_KEY: your-api-key"

# Weekly candles for 2025, by slug
curl -X GET "https://pro-api.coinmarketcap.com/v5/cryptocurrency/ohlcv/historical?slug=ethereum&interval=1w&time_start=2025-01-01&time_end=2025-12-31" \
  -H "X-CMC_PRO_API_KEY: your-api-key"
```

### Example Response

```json
{
  "data": [
    [
      78559.1131265811,
      78748.50143864893,
      78549.03085271013,
      78727.98306878311,
      30034635342.11,
      1580696743839.83,
      "2026-09-01T00:00:00.000Z",
      "2026-09-01T00:12:00.000Z",
      "2026-09-01T00:01:00.000Z",
      "2026-09-01T00:14:59.999Z"
    ],
    [
      78737.76559810448,
      78881.55883757334,
      78737.76559810448,
      78854.57252549069,
      30041940429.35,
      1583238401001.9,
      "2026-09-01T00:15:00.000Z",
      "2026-09-01T00:28:00.000Z",
      "2026-09-01T00:15:00.000Z",
      "2026-09-01T00:29:59.999Z"
    ]
  ],
  "status": {
    "timestamp": "2026-11-11T10:18:24.323Z",
    "error_code": "0",
    "error_message": "",
    "elapsed": 31,
    "credit_count": 1
  }
}
```

### Important Notes

1. **Plans**: Startup and above. Intraday (`15m`, `1h`, `4h`) history: Startup 1 month, Growth 3 months, Professional 12 months, Enterprise from 2010. Daily (`1d`, `1w`, `1M`) history from 2010 on every plan that has the endpoint.

2. **Credit usage**: 1 credit per 100 candles returned, rounded up. A request that returns no candles costs 0 credits.

3. **Time alignment**: Candles align to UTC boundaries. `1d` candles open at 00:00 UTC, `1w` on Mondays, `1M` on the first day of the month.

4. **Completed periods only**: Each candle is returned once its period has closed.

5. **Volume calculation**: Volume is total trading volume across all exchanges tracked by CMC during the period.

6. **Missing data**: Handle `null` values in a candle row in your code.
