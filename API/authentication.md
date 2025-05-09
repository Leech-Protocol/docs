---
label: Authentication
order: 10
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
