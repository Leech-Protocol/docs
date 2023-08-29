---
label: Leech Hedging strategy (coming soon)
order: 94
---


The main advantage of this strategy is that it creates a regular income from an asset chosen by the user, while it lessens the chance of losing the value of the chosen asset due to rebalancing and hedging mechanics.

How will it work? The user chooses a main asset, such as ETH, USDC, BTC,  etc., and deposits liquidity. Our system swaps liquidity, bridges it (if needed), and distributes it into a defined pool list. For instance, if a user picks ETH as their main asset, the system gives liquidity to different pools like ETH/GEAR, ETH/MATIC, and ETH/wUSDR, etc.

At the same time, the system keeps track of changes in asset prices, APR, liquidity, etc... If the system identifies a specific situation that can negatively affect the profitability or portfolio value, it makes the rebalance in pools and opens a short position to hedge asset volatility. This helps protect the main asset (like ETH) from impermanent loss and from losses of portfolio value. The result is that the returns come in the main asset, creating a steady income stream on all market trends.

We proved the efficiency of the hedging strategy with backtests and on-chain tests. These tests looked at strategy behavior on historical data and how well the rebalancing module performed in real pools on the Polygon network.
