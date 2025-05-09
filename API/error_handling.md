---
label: Error Handling
order: 7
---

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
