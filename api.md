# 📊 RM Light API

Real-time range-probability model for CEX markets.

**Base URL**

```
https://rm-cex.leechprotocol.com
```

**Endpoint**

```
POST /api/v1/probability/lite
```

**Headers**

```
Content-Type: application/json
```

**Request example**

```json
{
  "mode": "cex",
  "symbol": "ETHUSDC",
  "range": 0.01,
  "steps": 120,
  "interval": "min"
}
```

**curl**

```bash
curl -X POST "https://rm-cex.leechprotocol.com/api/v1/probability/lite" \
  -H "Content-Type: application/json" \
  -d "{\"mode\":\"cex\",\"symbol\":\"ETHUSDC\",\"range\":0.01,\"steps\":120,\"interval\":\"min\"}"
```

**Response example**

```json
{
  "probability": 0.609701
}
```

**Meaning**

`probability` = the chance that price **stays inside** the selected range.

| Field | Example | Reads as |
|---|---|---|
| `range` | `0.01` | ±1% band |
| `steps` | `120` | 120 minutes |
| `probability` | `0.61` | ~61% chance to stay inside ±1% over the next 120 minutes |

**Limits**

| Parameter | Range |
|---|---|
| `steps` | 5 – 360 |
| `range` | 0.001 – 0.05 |
| `interval` | use `"min"` |

<br>

> 💬 **Live signals on Telegram** — see detailed, real-time RM signals in the
> bot: **[@lpcex](https://t.me/lpcex)**

<br>

---
---

<br>

# 📈 RM Signals — Examples `[TEST]`

*Live illustrations of how the Range Model (RM) reads market volatility.
Each example pairs a **Signal** (the call) with a **Check** (what actually happened).*

<br>

---

## 🌊 Example 1 — Market Calm

<br>

### 🌊 RM Signal — Market Calm

**ETHUSDC** · `2026-06-27 06:59 UTC` · model `cal_sigma13`

> The market has moved into a **quieter-than-usual** zone. The model sees a
> somewhat higher chance that ETHUSDC stays inside its narrow ranges over the
> next few hours — elevated, but not yet at extreme levels.

**Main range**

| | |
|---|---|
| Price now | `1579.82` |
| Checked range | `1571.92 – 1587.72` |
| Band / window | `±0.50%` / `120m` |

**Key deviation**

| Band / window | Now | 1y normal |
|---|---|---|
| `±0.50%` / `120m` | **26.6%** | 1.0% |

**Meaning** — Volatility is on the calmer side. Range / grid / LP-style setups
may find this context useful. RM does **not** predict direction.

`Cooldown: 240m`

<br>

### ✅ RM Check — Calm Phase Held

**ETHUSDC** · `2026-06-27 08:59 UTC` · model `cal_sigma13`

**Original signal:** `DEEP_COMPRESSION / soft`

| | |
|---|---|
| Band / window | `±0.50%` / `120m` |
| RM no-touch estimate | 26.6% |
| Observed | **Held** ✅ |
| Largest move from signal price | `+0.25%` / `−0.13%` |
| Closed at | `+0.10%` (`1581.46`) |

**Outcome** — The market stayed inside the checked range. This **supports** the
earlier calm-phase signal: realised movement was limited during the window.

> 📝 **Reading it:** Staying inside a ±0.50% band over 120 minutes is normally
> rare — about **1%** of the time on a 1-year basis. Here the model puts it at
> **26.6%**, a sharp jump that reads as an unusually calm, low-energy market.

<br>

---
---

<br>

## ⚡ Example 2 — Extreme Market Move

<br>

### ⚡ RM Signal — Extreme Market Move

**ETHUSDC** · `2026-06-26 06:48 UTC` · model `cal_sigma13`

> The move is **outside the 1-year historical extreme band** for at least one
> monitored cell.

**Main range**

| | |
|---|---|
| Price now | `1567.68` |
| Checked range | `1552.00 – 1583.36` |
| Band / window | `±1.00%` / `120m` |

**Key deviation**

| Band / window | Now | 1y normal |
|---|---|---|
| `±1.00%` / `120m` | **9.4%** | 38.0% |

**Meaning** — Range-bound conditions are deteriorating. Be careful with fresh
range entries and review risk on existing range / grid / LP-style positions.
RM does **not** predict direction.

`Cooldown: 60m`

<br>

### 🎯 RM Check — Extreme Move Confirmed

**ETHUSDC** · `2026-06-26 08:48 UTC` · model `cal_sigma13`

**Original signal:** `MARKET_EXPANSION / extreme`

| | |
|---|---|
| Band / window | `±1.00%` / `120m` |
| Predicted no-touch | 9.4% |
| Observed | **Touched** ⚡ |
| Range broke after | `50m` |
| Largest move from signal price | `+1.02%` / `−0.24%` |
| Closed at | `−0.19%` (`1564.73`) |

**Outcome** — Price broke out of the checked range within the horizon — the
extreme-move prediction was **confirmed**.

> 📝 **Reading it:** Holding inside a ±1.00% band over 120 minutes normally
> happens about **38%** of the time on a 1-year basis. Here that probability
> collapsed to just **9.4%** — the market followed through and the range
> **broke after 50 minutes**.

<br>

---

> ### ⚠️ Note
> These are **illustrative examples** built on a **small set** of monitored
> bands and time windows. Via the API you can query the full grid:
> **time steps from 5 to 360 minutes** and **bands from ±0.5% to ±5%**.
