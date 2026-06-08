# Short-Strangle-group-project
TLDR: NIFTY Weekly Short-Strangle strategy conditioned on India VIX × Days-to-Expiry. Course project by Veho Bogi and Manan Moraje for Financial Analysis using AI — Sharpe 2.78, 19.2% CAGR, 67% win rate over 7 years.



## Overview

This project designs, backtests, and validates a fully rule-based algorithmic options-selling strategy on NIFTY weekly expiry contracts. The core innovation is **adaptive parameter selection**: rather than fixing a single strangle offset and stop-loss across all market conditions, parameters are conditioned on two observable regime signals — **India VIX** and **Days-to-Expiry (DTE)** — producing a 6×5 grid of individually optimised (offset, SL) pairs.

**7-Year Backtest Summary (2019–2025) | ₹1,21,000 Starting Capital**

| Metric | 2D Adaptive (Flat) | 2D Adaptive (Compounded) |
|---|---|---|
| CAGR | 19.2% | 39.7% |
| Sharpe Ratio | 2.78 | 2.13 |
| Calmar Ratio | 2.98 | 1.95 |
| Max Drawdown | −6.5% | −20.3% |
| Win Rate | 67% | — |

---

## Strategy Design

**Base Strategy**
- Short Strangle on NIFTY weekly expiry contracts (simultaneous sale of OTM Call and OTM Put)
- Entry at 09:20, Exit at 15:15
- 18 parameter combinations tested: 3 offsets (±50, ±100, ±150 points) × 6 stop-loss levels (25%–50%)

**Why Adaptive?**
No single fixed (offset, stop-loss) combination dominates across all market regimes. The 2D adaptive approach conditions both parameters on market signals to maximise risk-adjusted returns.

**1D Adaptive Rule — VIX → Stop-Loss**

| VIX Regime | Stop-Loss |
|---|---|
| < 15 | 30% |
| 15–22 | 40% |
| 22–28 | 30% |
| ≥ 28 | 40% |

**2D Adaptive Rule — VIX × DTE → Offset + Stop-Loss**

Extends the 1D rule by conditioning the strangle offset on Days-to-Expiry as well. For each of the 30 (VIX bucket × DTE bucket) cells, the top-K ensemble (K=5) of configurations ranked by Sharpe ratio is used, subject to a minimum 30-trade filter per cell.

---

## Validation

- **Walk-Forward Validation** — out-of-sample testing across rolling time windows
- **Monte Carlo Simulation** — 10,000 block-bootstrap simulations (block size = 20) across 5-year horizons, comparing full-history vs regime-weighted sampling pools
- All P&L figures sourced from **StockMock**, incorporating realistic execution costs (slippage, brokerage)

**Monte Carlo Results (5-Year Horizon, 10,000 simulations)**

| Scenario | Median CAGR | 5th/95th CAGR | P(Equity > Start) |
|---|---|---|---|
| Full History | ~18–20% | varies | >90% |
| Regime-Weighted (recent 2×) | ~20–22% | varies | >90% |

---

## Risks & Limitations

- **Regime Change** — VIX bucketing derived from 2019–2025 data; structural volatility shifts may invalidate learned rules
- **Gap Risk** — Weekend/overnight gaps and event-driven moves (budgets, elections) can breach stop-losses before market open
- **Liquidity Risk** — Far-OTM weekly strikes may suffer wide spreads in stressed markets; StockMock costs may underestimate impact
- **Rule Drift** — Periodic recalibration (e.g. rolling 2-year re-fit) is advisable as microstructure evolves
- **Concentration Risk** — Single underlying (NIFTY), single instrument structure (short strangle)

---

## Technical Stack

- Python (NumPy, Pandas, Matplotlib)
- StockMock for backtesting with realistic execution costs
- Block-bootstrap Monte Carlo simulation
- VIX × DTE regime-conditioning framework

---

## Authors

**Veho Bogi** and **Manan Moraje**
Course: Financial Analysis using Artificial Intelligence
