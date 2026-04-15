# Hyperliquid Trader Behavior × Bitcoin Fear/Greed Sentiment Analysis

## Overview

This project analyzes how market sentiment (Bitcoin Fear/Greed Index) correlates with trader behavior and performance on the Hyperliquid DEX. It covers full data preparation, behavioral segmentation, performance analysis, and actionable strategy derivation.

---

## Project Structure

```
.
├── hyperliquid_sentiment_analysis.ipynb   # Main analysis notebook
├── fear_greed_index.csv                   # Bitcoin Fear/Greed Index (2018–2025)
├── historical_data.csv                    # Hyperliquid trade history (2023–2025)
├── README.md
└── outputs/
    ├── chart1_performance_by_sentiment.png
    ├── chart2_behavior_by_sentiment.png
    ├── chart3_segment_heatmaps.png
    ├── chart4_cumulative_pnl.png
    ├── chart5_longshort_vs_sentiment.png
    ├── chart6_trade_size_distribution.png
    ├── chart7_coins_by_sentiment.png
    └── chart8_feature_importance.png
```

---

## Setup

### Requirements

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn
```

### How to Run

1. Place both CSV files (`fear_greed_index.csv`, `historical_data.csv`) in the same directory as the notebook.
2. Launch Jupyter:
   ```bash
   jupyter notebook hyperliquid_sentiment_analysis.ipynb
   ```
3. Run all cells top-to-bottom (`Kernel → Restart & Run All`).

Charts will be saved as `.png` files in the working directory.

---

## Datasets

| Dataset | Rows | Columns | Period |
|---|---|---|---|
| fear_greed_index.csv | 2,644 | 4 | 2018-02-01 → 2025-05-02 |
| historical_data.csv | 211,224 | 16 | 2023-05-01 → 2025-05-01 |

**Key columns used:**
- `Timestamp IST` — trade datetime (DD-MM-YYYY HH:MM)
- `Account` — trader wallet address (32 unique)
- `Closed PnL` — realised profit/loss per closing trade
- `Size USD` — position size in USD
- `Direction` — Open Long / Close Long / Open Short / Close Short / Sell / Buy
- `classification` — Extreme Fear / Fear / Neutral / Greed / Extreme Greed

---

## Methodology

1. **Data Cleaning** — parsed timestamps, removed unparseable rows, renamed columns, computed `net_pnl = Closed PnL − Fee`.
2. **Alignment** — merged trade data to Fear/Greed index on calendar date (inner join).
3. **Closing trade isolation** — PnL computed only on closing directions to avoid double-counting.
4. **Daily aggregation** — PnL, win rate, trade count, L/S ratio aggregated per day.
5. **Trader segmentation** — 3 segments: size tier, frequency tier, winner/loser.
6. **Statistical testing** — two-sample t-test for PnL difference between Fear vs Greed days.
7. **Predictive model** — Random Forest classifying next-day collective profitability.

---

## Key Insights

| # | Insight |
|---|---|
| 1 | **Greed days deliver higher PnL and win rate** — traders close profitable positions more frequently when sentiment is positive. |
| 2 | **Directional bias shifts with sentiment** — long/short ratio rises on Greed days; traders lean short or neutral on Fear days. |
| 3 | **Bigger positions on Greed days** — median trade size is larger during Greed (FOMO effect), increasing risk. |
| 4 | **High-leverage traders are most exposed on Fear days** — they show the greatest drawdowns precisely when sentiment is negative. |
| 5 | **FGI value and prior win rate are the top model features** — sentiment is a genuine leading signal for next-day aggregate profitability. |

---

## Strategy Recommendations

### Strategy 1 — Sentiment-Gated Position Sizing
> **Rule:** Reduce position size by 30–50% when FGI < 40. Scale up only when FGI > 55.

**Rationale:** Fear days exhibit lower win rates and negative avg PnL. Losers' worst drawdowns occur because they maintain or increase size on Fear days. Winners naturally size down — formalising this rule protects capital.

**Applies to:** All traders, especially the High-Leverage segment.

---

### Strategy 2 — Directional Bias Rule
> **Rule:** Favour longs on Greed days (FGI > 60). On Fear days (FGI < 40), prefer shorts or stay flat — avoid chasing longs.

**Rationale:** L/S ratio structurally rises during Greed. Long positions opened on Greed days yield substantially higher PnL than those opened on Fear days. Frequent traders who ignore this show a sharp win rate drop on Fear when maintaining long bias.

**Applies to:** Frequent traders and moderate-leverage segment.

---

## Output Charts

| Chart | Description |
|---|---|
| chart1 | PnL, win rate, and distribution by all 5 sentiment classes |
| chart2 | Trade frequency, size, and L/S ratio by sentiment |
| chart3 | Segment heatmaps (size tier, freq tier, winner/loser) |
| chart4 | Cumulative PnL over time — Winners vs Losers |
| chart5 | Long/Short ratio vs Fear/Greed index over time |
| chart6 | Trade size distribution — Fear vs Greed days |
| chart7 | Most traded coins by sentiment regime |
| chart8 | Feature importances for next-day profitability model |
