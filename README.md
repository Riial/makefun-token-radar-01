# Token Radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/Riial/makefun-token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/Riial/makefun-token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Entry for the [make.fun Token Radar bounty](https://make.fun/bounty/token-radar-01): a token-launch radar that ranks pre-token protocols by implied value, straight from the sealed DefiLlama snapshot.

## What it is

The radar reads `/input/snapshot.json`, scores every pre-token protocol, and writes a ranked board (`/output/board.json`) plus a static dashboard (`/output/dashboard/`) and three screenshots. No network, no external data: the model uses only the snapshot's own figures.

## The model (radar-2)

```
implied = round( tvl_usd × category_multiple × growth_mult × maturity_mult × chain_mult × tier_mult )
```

- **category_multiple** — the median market-cap-to-TVL ratio of the live-token protocols in the same category, read off the snapshot itself (clamped to [0.05, 1.00]); categories with fewer than 3 live comparables fall back to the market median (0.246).
- **growth_mult** = 1 + clamp(change_7d, −60, 120) / 100 × 0.5
- **maturity_mult** = 0.6 + min(age_days, 1095) / 1095 × 0.4 (unknown age: 365 days)
- **chain_mult** = 1 + 0.02 × min(chains − 1, 10)
- **tier_mult** = 1.10 at $1B+ TVL, 1.05 at $100M+, else 1.00

The board covers the whole 40-slug watchlist (TVL ≥ $50M, no token) plus the 160 highest-TVL early pre-token protocols below the floor — 200 rows total. Every row carries its own farm plan: category action, chain, capital tier ($50–$250) and hold time (30–60 days).

## The dashboard

A dark, static page with no framework and no network: a sortable board, filters (search, category, chain, watchlist/early, minimum TVL), a detail view per protocol with the full implied-value breakdown, a live-token comparison table, and a methodology section with the calibration table. State lives in the URL hash (`#p=slug`, `#category=`, `#chain=`, `#q=`, `#scope=`, `#tab=`).

## Run it

```sh
node main.ts --input input/snapshot.json --output out
```

Run twice and the outputs are byte-identical. Screenshots live in `screenshots/` (board, detail, filtered view).
