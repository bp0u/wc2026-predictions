# FIFA World Cup 2026 — Match Prediction System

Predicts outcomes for the remaining matches of the 2026 FIFA World Cup (Round of 32 onward) using a Dixon-Coles Poisson model and an XGBoost classifier, validated by backtesting on the 2018 and 2022 tournaments, then projected forward with a 10,000-run Monte Carlo simulation of the bracket.

## What it does

1. **Pulls data** from free, public, no-key-required sources:
   - Historical international results (1872–present) — [`martj42/international_results`](https://github.com/martj42/international_results)
   - Live 2026 World Cup fixtures/results — [`openfootball/worldcup.json`](https://github.com/openfootball/worldcup.json)
2. **Engineers features** in a single leak-free chronological pass: Elo rating difference, recent form, head-to-head record, rest days, neutral-venue flag.
3. **Trains two models**:
   - Dixon-Coles Poisson (primary) — predicts full scoreline distributions
   - XGBoost (comparison) — predicts Win/Draw/Loss directly
4. **Backtests** both models on the actual 2018 and 2022 World Cups before trusting them on 2026.
5. **Simulates the bracket** 10,000 times to estimate each team's probability of reaching each round and winning the tournament.
6. **Exports** clean prediction tables and a title-odds chart.

## Setup

```bash
git clone https://github.com/bp0u/wc2026-predictions.git
cd wc2026-predictions
pip install -r requirements.txt
jupyter notebook wc2026_predictions.ipynb
```

Run all cells top to bottom. The notebook fetches live data on each run, so predictions update as the tournament progresses. If a fetch fails (rate limit, no internet), it falls back to a local copy of the same filename — see Section 1 for details.

## Output

- `wc2026_match_predictions.csv` — per-fixture Win/Draw/Loss % and predicted scoreline
- `wc2026_bracket_probabilities.csv` — per-team probability of reaching each round / winning it all
- `wc2026_title_odds.png` — bar chart of title odds for the top contenders

## Tuning

All key assumptions (Elo parameters, form window, Dixon-Coles decay, Monte Carlo iterations, etc.) are summarized in a table at the end of the notebook with the section where each lives.

## Disclaimer

Built for fun/learning. Not betting advice — these are statistical estimates from a simplified model, not certainties.
## Disclaimer

This is a portfolio/learning project, not a betting tool. Football outcomes have a large
irreducible random component — even a well-calibrated model gets plenty of individual matches
"wrong."
