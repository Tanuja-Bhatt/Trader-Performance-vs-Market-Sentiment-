# Trader Performance vs Market Sentiment — Hyperliquid × Fear and Greed Index

## Overview

This project explores how Bitcoin market sentiment (Fear/Greed) correlates with trader behavior and performance on Hyperliquid. The dataset spans **May 2023 – May 2025**, covering **211,218 trades** across **32 unique accounts**.

---

## Setup & How to Run

### Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

### Data

Place the following files in the same directory as the notebook:

- `compressed_csv/historical_data.csv` — Hyperliquid trade history
- `fear_greed_index.csv` — Bitcoin Fear & Greed Index

### Running

```bash
jupyter notebook analysis_notebook.ipynb
```

Or run as a plain script if preferred:

```bash
python analysis_script.py
```

---

## Methodology

**Data Preparation**
- Trader timestamps were in `dd-mm-yyyy HH:MM` (IST) format and converted to UTC-normalized dates for daily alignment
- Fear & Greed classifications were collapsed from 5 classes (*Extreme Fear, Fear, Neutral, Greed, Extreme Greed*) into 3 buckets (Fear / Neutral / Greed) for cleaner cross-group comparisons
- Key derived metrics: `net_pnl` (PnL − fee), `is_winner`, `is_long`, `long_ratio`, `daily_pnl`, `drawdown_proxy`
- Daily per-account aggregates were computed before any sentiment analysis to prevent trade-count imbalance from skewing results

**Analysis approach**
- Group-level comparisons of performance and behavior metrics across Fear / Neutral / Greed days
- Segment analysis across three cuts: performance bucket (win rate), position size bucket, and trade frequency bucket
- KMeans clustering (k=3) on standardised trader-level features to identify behavioral archetypes
- Random Forest classifier for next-day profitability prediction using sentiment + session-level features

---

## Key Insights

### 1. Fear Days Produce Higher Average PnL (but Lower Win Rate)

Fear days average **$5,038 daily PnL per trader**, compared to **$4,067 on Greed days** and $3,334 on Neutral days. This is counterintuitive but explainable: win rates are marginally lower on Fear days (35.7% vs 36.3%), but traders take larger positions (~$8,530 avg vs ~$5,955) and trade more frequently (~105 trades/day vs ~77). A few outsized winning trades on high-volatility Fear days pull the average up.

### 2. Contrarian Long Bias Emerges During Fear

Long ratio climbs to **52.2% on Fear days**, compared to **47.2% on Greed days**. Traders are systematically buying dips during fear-driven selloffs — and on aggregate, they're being rewarded for it. This "buy the fear" pattern is consistent across most trader segments.

### 3. Inconsistent Traders Bleed on Greed Days

Traders with sub-40% win rates show their worst drawdown behavior during Greed periods: they increase position sizes chasing momentum but fail to capitalise, resulting in consistent negative expected daily PnL. The gap between consistent winners and inconsistent traders is widest on Greed days.

---

## Strategy Recommendations

**Strategy 1 — "Buy the Fear" Rule for Active Traders**  
On days when the Fear & Greed Index drops below 40, allow a **1.3× position size multiplier** for accounts with a trailing 30-day win rate above 50%. Historical data shows these accounts generate meaningfully higher PnL during Fear sessions.

**Strategy 2 — Greed-Day Position Cap for Inconsistent Accounts**  
During Greed periods (F&G > 60), apply a **0.7× position size cap** to accounts with sub-40% win rate. These traders over-extend during bullish sentiment and accumulate losses. Automated flagging at the account level can enforce this without manual intervention.

---

## Outputs

| File | Description |
|------|-------------|
| `analysis_notebook.ipynb` | Full annotated notebook |
| `charts/chart1_performance_by_sentiment.png` | Avg PnL, win rate, drawdown by sentiment |
| `charts/chart2_behavior_by_sentiment.png` | Trade freq, long ratio, position size by sentiment |
| `charts/chart3_segment_analysis.png` | PnL and win rate by segment × sentiment |
| `charts/chart4_timeseries_pnl_vs_fg.png` | Time-series overlay of daily PnL and F&G index |
| `charts/chart5_clustering.png` | Trader archetype clusters |
| `charts/chart6_feature_importance.png` | Predictive model feature importances |
| `table1_sentiment_performance.csv` | Summary stats table by sentiment |
| `table2_cluster_profiles.csv` | Cluster mean profiles |
