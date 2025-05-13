---
label: API
order: 140
icon: 
---


Currently, the API does not implement authentication. For production or public-facing deployments, we strongly recommend integrating a secure method (e.g., API keys, JWT tokens) to control access.

### Base URL & Environment Variables

```http://164.90.156.209/api/testing```

### Delta Calculation

Delta is a volatility measure used to model price uncertainty over time. It represents the average absolute percentage change in price over a given window.

### Step-by-step:

Step 1: For each pair of consecutive average prices P[t] and P[t+1], compute the absolute percentage change:

```delta_t = abs((P[t+1] - P[t]) / P[t])```

Mathematically:

```deltaₜ = |P[t+1] - P[t]| / P[t]```

Step 2: Take a rolling average of delta_t over a window (default: averaging_range = steps / 2). This smooths out short-term noise:

```rolling_delta = delta_series.rolling(window=averaging_range).mean()```

### Endpoints

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

### Datasets

The following historical datasets are available for testing and evaluating the prediction model. Each file contains minute-level price data for the ETH/USDC pair on Uniswap.

#### 1.uniswap_eth_usdc_minute_2023-08-01_2024-07-31.csv
**Period**: August 1, 2023 → July 31, 2024 <br>
**Chain**: Ethereum <br>
**Download**: <a href="https://drive.google.com/file/d/1jToHNGLBvACRTSrA1LZidmCAubCNBIV4/view?usp=sharing" target="_blank">📥 Google Drive Link</a>

#### 2. uniswap_eth_usdc_minute_2024-12-31_2025-04-14.csv
**Period**: December 31, 2024 → April 14, 2025 <br>
**Chain**: Ethereum <br>
**Download**: <a href="https://drive.google.com/file/d/1jbf7wE79uUgyjiftNjtRMzQzYAQCKZ1f/view?usp=sharing" target="_blank">📥 Google Drive Link</a>

### Error Handling

If an error occurs, the API returns an HTTP 500 status with an error message in JSON format:
```json
{
  "error": "Description of error."
}
```

Check the server logs for more details if you encounter issues.


### Examples:

Test 1:
Request

```json
{
    "dataset": "uniswap_eth_usdc_minute_2024-12-31_2025-04-14.csv",
    "steps": 240,
    "range": 0.01
}
```

Response

```json
{
    "debug": {},
    "non_static": {
        "average_probability": 42.08178131360263,
        "cases_prob_over_80": 8789,
        "cases_prob_under_1": 9900,
        "total": 61679
    },
    "overall": {
        "average_probability_within_range": 0.5067226948098416,
        "high_confidence": {
            "range_in_accuracy_percentage": 74.21143745782108,
            "range_in_total": 34081,
            "range_in_wins": 25292,
            "range_out_accuracy_percentage": 68.0225367596537,
            "range_out_total": 14554,
            "range_out_wins": 9900
        },
        "naive": {
            "in_wins": 81552,
            "naive_accuracy_in_range_percentage": 56.93739483770971,
            "naive_accuracy_out_range_percentage": 43.06260516229029,
            "total": 143231
        },
        "processed_rows": 143231
    },
    "static": {
        "average_probability": 57.16938444660028,
        "cases_prob_over_80": 25292,
        "cases_prob_under_1": 4654,
        "total": 81552
    }
}
```

Test 2:

Request

```json
{
    "dataset": "uniswap_eth_usdc_minute_2024-12-31_2025-04-14.csv",
    "steps": 120,
    "range": 0.005
}
```

Response

```json
{
    "debug": {},
    "non_static": {
        "average_probability": 42.14056895283416,
        "cases_prob_over_80": 304,
        "cases_prob_under_1": 4645,
        "total": 77410
    },
    "overall": {
        "average_probability_within_range": 0.46652023281710875,
        "high_confidence": {
            "range_in_accuracy_percentage": 65.33637400228051,
            "range_in_total": 877,
            "range_in_wins": 573,
            "range_out_accuracy_percentage": 80.40505452657088,
            "range_out_total": 5777,
            "range_out_wins": 4645
        },
        "naive": {
            "in_wins": 66061,
            "naive_accuracy_in_range_percentage": 46.04484529974699,
            "naive_accuracy_out_range_percentage": 53.95515470025301,
            "total": 143471
        },
        "processed_rows": 143471
    },
    "static": {
        "average_probability": 51.938526356117066,
        "cases_prob_over_80": 573,
        "cases_prob_under_1": 1132,
        "total": 66061
    }
}
```
