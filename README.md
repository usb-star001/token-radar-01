[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/usb-star001/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/usb-star001/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

# Token Launch Radar

A radar for pre-token protocols: which ones record no token yet, how much implied
value a token would carry, and how to farm each one before any launch.

Built for make.fun's **Build the best token launch radar** bounty
(`token-radar-01`), $1,325 in USDC, 1st place $1,000.

## What it does

- Scores every rankable pre-token protocol in the snapshot (758 protocols,
  40 on the watchlist, 198 rankable pre-token rows after excluding
  wrapped/staked products).
- Ranks them by implied value, with the model broken down per protocol.
- Gives each protocol an 8-step farm plan with concrete costs and times.
- Keeps live-token protocols in a separate tab, with their market caps.

## The model — radar-2

```
implied = TVL x category_multiple x growth_mult x maturity_mult x chain_mult
```

- `category_multiple` — different business models capture a different share
  of TVL as token value: Dexs / Perpetual / Derivatives 0.45, Lending / CDP /
  Collateral 0.35, Yield / Leveraged Farming / Liquidity Manager 0.30,
  RWA / Stablecoin / Payments 0.20, Risk Curators / Onchain Capital
  Allocator 0.15, Chain / Bridge / other 0.12, default 0.25.
- `growth_mult = 1 + clamp(change_7d, -40, +80) / 100 x 0.7` — 7-day TVL
  growth, clamped so one hot week cannot dominate.
- `maturity_mult = 0.65 + 0.35 x min(age_days, 1095) / 1095` — how long the
  protocol has run with no token; unknown age counts as 365 days.
- `chain_mult = 1 + 0.04 x min(chains - 1, 3)` — protocols live on more
  chains reach more users.

The model uses only snapshot fields. No protocol is special-cased by name.
A protocol is said to "record no token", never that it has none — the
snapshot only records what it records. Implied value is the model's own
estimate, not a prediction that a token will issue, or when, or at what price.

Top of the board (snapshot 2026-11-14):

1. Steakhouse Financial — $375,185,730 implied
2. Sentora Curator — $367,384,806 implied
3. USDD — $330,722,796 implied

## Farm plans

Each row carries 8 steps: fresh-wallet hygiene, funding/bridging with the
chain's gas cost, the core category action, a second different qualifying
action, a weekly cadence, a hold period (60/45/30 days by age), claim safety
(only from the protocol's own published link, never pay to claim), and a farm
log plus unwind. Every step states its cost and time. Capital is tiered by
TVL: $250 at $5B+, $150 at $1B+, $100 at $100M+, otherwise $50.

## Run it

Node 22, standard library only, no network:

```sh
MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

That writes `board.json`, `data.js`, `dashboard/`, `screenshots/` and
`dossier.json` into `out/`. Running it twice produces identical bytes.

## Dashboard

`dashboard/` is a static page (no network, no external assets): sortable
table, search, category and chain filters, watchlist-only toggle, minimum
TVL, a detail view per protocol with the full model breakdown and its farm
plan, a live-token tab, and a "How the model works" section.

## License

MIT. See [LICENSE](LICENSE).
