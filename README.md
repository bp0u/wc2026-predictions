# FIFA World Cup 2026 Prediction System

A from-scratch prediction pipeline for the remaining matches of the 2026 World Cup, built around
a **Dixon-Coles Poisson model** (primary) and an **XGBoost classifier** (comparison), backtested
on the real 2018 and 2022 tournaments before being trusted on 2026.

## What it does

1. **Data** — pulls historical international results (1872–2026), live 2026 fixtures/results, and
   computes its own Elo ratings (eloratings.net isn't scrape-friendly, so this replicates their
   published methodology from the historical results instead).
2. **Features** — Elo difference, recent form (last 10 matches goal diff), head-to-head record,
   neutral-venue flag, rest days — all built leak-free via a single chronological pass.
3. **Models** — Dixon-Coles predicts the full scoreline probability grid for any matchup;
   XGBoost predicts Win/Draw/Loss directly as a sanity check.
4. **Backtest** — both models are refit using only data available *before* the 2018 and 2022
   World Cups and scored against what actually happened.
5. **Live application** — predicts every currently-scheduled knockout fixture.
6. **Monte Carlo** — 10,000 simulated playthroughs of the rest of the bracket to estimate each
   team's probability of reaching each round and winning the tournament.

## Results (as of the last run)

| Model | 2018 WC accuracy | 2018 WC log-loss | 2022 WC accuracy | 2022 WC log-loss |
|---|---|---|---|---|
| Dixon-Coles | ~44% | ~0.95 | ~47% | ~1.08 |
| XGBoost | ~47% | ~1.03 | ~48% | ~1.07 |
| Baseline (uniform 1/3-1/3-1/3) | 33% | 1.099 | 33% | 1.099 |

(3-class football outcome prediction is genuinely hard — beating the uniform baseline on
log-loss is the bar that matters more than raw accuracy.)

![Title odds](outputs/wc2026_title_odds.png)

## Running it

```bash
pip install -r requirements.txt
jupyter notebook wc2026_predictions.ipynb
```

All data is fetched live from public sources at runtime — no API keys required. If a source is
temporarily unreachable, each fetch cell has a local-file fallback (see Section 1 of the notebook).

## Tunable assumptions

Every modeling assumption (Elo K-factors, time-decay rate, training window, penalty-shootout
model, Monte Carlo iteration count, etc.) is called out in code comments and summarized in a
table at the end of the notebook.

## Disclaimer

This is a portfolio/learning project, not a betting tool. Football outcomes have a large
irreducible random component — even a well-calibrated model gets plenty of individual matches
"wrong."
