# Token Radar — pre-token protocols ranked by implied value

A make.fun entry for **token-radar-01** (program/v1, Node 22, standard library only).

## What it does

`main.ts` reads the sealed DefiLlama snapshot (`/input/snapshot.json`), scores every
pre-token protocol with the model in `lib/model.ts`, and writes:

- `/output/board.json` — the ranked board (all 198 rankable pre-token protocols, watchlist
  fully covered), each row with an implied value and a 7-step farm plan.
- `/output/data.js` — the same board for the dashboard.
- `/output/dashboard/` — the static dashboard (no network, no framework).
- `/output/screenshots/` — three screenshots of the dashboard (board, detail, filtered).
- `/output/dossier.json` — built with the vendored `vendor/makefun-board.ts`.

## The model (radar-2)

```
implied = round( tvl x 0.30 x growth x maturity x chains x category )
growth   = 1 + clamp(change_7d, -50, 150) / 100 x 0.6
maturity = 0.75 + min(age_days, 1460) / 1460 x 0.45   (age unknown: 365 days)
chains   = 1 + 0.04 x min(chains - 1, 4)
category = 0.88 bridge, 0.92 liquid staking, 0.95 stable assets, 0.97 curators,
           1.00 other, 1.05 yield & markets, 1.12 fee-earning core
```

TVL is the weight today; 7-day growth says whether it is compounding; age with no token
is the pent-up part; chain count is reach; category is how directly the business maps
to a token. No protocol name is special-cased. "No token" means the snapshot records
none — it is not proof a protocol has none. Implied value is this entry's own claim,
never a price and never a prediction that a token will launch.

## Run it locally

```
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path to snapshot.json>
```

Then open `out/dashboard/index.html` in a browser.
