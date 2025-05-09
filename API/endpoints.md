---
label: Endpoints
order: 9
---

### POST /calculate-single-probability

### Description

Calculates the probability that a given asset's price will remain within a specified range (± threshold) for a certain number of time steps, using manually provided inputs.

Request
* Methods: POST
* URL :```/calculate-single-probability```

### Request Body (JSON)

| Field         | Type    | Description                                      | Example   |
|---------------|---------|:-------------------------------------------------|----------:|
| currentPrice  | float   | Current price of the asset.                      | 2100.25   |
| delta         | float   | Volatility measure (absolute delta; capped at 0.05). | 0.0045    |
| steps         | integer | Prediction horizon (in minutes).                 | 120       |
| range         | float   | Target range threshold (e.g., 0.005 for ±0.5%).  | 0.005     |

### Response

### Success (HTTP 200)

```json
{
  "probability_within_range": 0.6781,
  "lower_bound": 2089.75,
  "upper_bound": 2150.75,
  "confidence_interval_80": {
    "lower": 2092.11,
    "upper": 2159.30
  }
}
```
* Probability_within_range: Predicted chance (in fraction) that the future price will be within the target range.
* Lower_bound / upper_bound: The target range based on the current price and the given threshold.
* Confidence_interval_80: 80% confidence interval computed from the underlying normal distribution.

### POST /calculate-current-market-probability

### Description

Computes the probability using live market data. This endpoint retrieves the latest price data, calculates the volatility measure, and then invokes the prediction algorithm.

### Request

* Method: POST
* URL: ```/calculate-current-market-probability```

### Request Body (JSON)

| Field | Type    | Description                                  | Example |
|-------|---------|----------------------------------------------|---------|
| steps | integer | Number of minutes ahead to run the prediction | 120     |
| range | float   | Range threshold (e.g., 0.005 for ±0.5%)      | 0.005   |

## Response

The response format is identical to```/calculate-single-probability.```

!!!Note: This endpoint automatically fetches live data in Uniswap ETH/USDC pool to determine the current price and volatility.
!!!
### POST /test-historical

## Description

This endpoint runs the prediction model across an entire historical dataset. In addition to returning overall metrics (e.g., average probability, accuracy, interval scores), it includes a debug section with detailed prediction data for selected timestamps.

### Request
* Method: POST
* URL: ```/test-historical```

### Request Body (JSON)

| Field    | Type   | Description                                          | Example                                        |
|----------|--------|------------------------------------------------------|------------------------------------------------|
| dataset  | string | Name of the dataset CSV file                         | uniswap_eth_usdc_minute_2023-08-01_2024-07-31.csv |
| steps    | int    | Prediction steps ahead (in minutes).                 | 120                                            |
| range    | float  | Threshold for the target range (e.g., 0.005 for ±0.5%). | 0.005                                          |
|          |        |                                                      |                                                |

## Response

```json
{
  "overall": {
    "processed_rows": 498189,
    "average_probability_within_range": 0.6473,
    "naive": {
      "total": 498189,
      "in_wins": 323268,
      "naive_accuracy_percentage": 64.89
    },
    "high_confidence": {
      "range_in_total": 39547,
      "range_in_wins": 30818,
      "range_in_accuracy_percentage": 77.93,
      "range_out_total": 50,
      "range_out_wins": 46,
      "range_out_accuracy_percentage": 92.00
    }
  },
  "static": {
    "total": 323268,
    "average_probability": 66.99,
    "naive_accuracy_percentage": 100.0,
    "cases_prob_over_80": 30818,
    "cases_prob_under_1": 4
  },
  "non_static": {
    "total": 174921,
    "average_probability": 60.52,
    "naive_accuracy_percentage": 0.0,
    "cases_prob_over_80": 8729,
    "cases_prob_under_1": 46
  },
  "debug": {
    "2023-12-28T09:55:00": {
      "initial_price": 2392.14,
      "future_price": 2402.47,
      "delta": 0.00074,
      "predicted_probability": 0.4480,
      "predicted_range": [2380.18, 2404.10],
      "target_range": [2380.18, 2404.10],
      "actual_in_range": true},
    "2024-03-25T20:27:00": {
      "initial_price": 3635.95,
      "future_price": 3606.98,
      "delta": 0.00046,
      "predicted_probability": 0.2429,
      "predicted_range": [3642.69, 3691.88],
      "target_range": [3617.77, 3654.13],
      "actual_in_range": false}
    // ... similar entries for other timestamps
  }
}
```

### Explanation

* Overall: Aggregated metrics for the entire dataset (naive predictions, high‑confidence predictions, etc.).
* Static / non_static: Breakdowns based on the actual behavior:
  * Static: Timeframes where the future price remained within the target range.
  * Non-static: Timeframes where the future price fell outside the target range.
* Debug: Optional. Contains detailed prediction data (e.g., for timestamps “2023-12-28T09:55:00”, “2024-03-25T20:27:00”, etc.) for advanced debugging and analysis. Debug cells setup manually in the code by request.
