# START HERE — worker orientation

You are a **data-pull worker** on a machine with **IBKR** (and Bloomberg) access. Your tasks are
published as markdown instruction files in THIS private GitHub repo by a coordinating session on
another machine.

## Operating model
- Pull this repo → read the current instruction file → execute it **exactly** → save outputs **LOCALLY**.
- This repo is **READ-ONLY for you: NEVER commit or push anything back.**
- The owner collects your output **out-of-band** (not via git).
- **Re-pull before each task** to get the latest instructions.

## Connect
```
git clone https://github.com/tjj333/flowtrading      # or, if already cloned:  cd flowtrading && git pull
```

## Current task — execute `ibkr_oos_pull.md` (in this repo)
An IBKR historical 5-min data pull to unblock the out-of-sample backtest. Reference fields are in
`bloomberg_fields.md`. Full detail (including the Tier-2 ticker lists) is in `ibkr_oos_pull.md` — read it.

Essentials:
- **TIER 1 first** (the core book, ~85% of P&L): Tokyo ords `285A.T 9984.T 8035.T 6857.T 6981.T 5803.T`,
  the `FJIKY` ADR, and benchmarks `SMH` + `ARM`. (Already have: KXIAY/SFTBY/TOELY/ATEYY/MRAAY ADRs, NKD_F, ES_F, USDJPY.)
- **CRITICAL — windowing:** Tokyo ords on the **TSE regular cash session** (09:00–11:30 + 12:30–15:00 JST);
  every trading day **must** include the **open bars (09:00–09:30 JST = 00:00–00:30 UTC)** and **close bars
  (14:30–15:00 JST = 05:30–06:00 UTC)**. Full session — **NOT "firstlast"** (the prior pull was mis-windowed:
  bars at 14:00–16:00 UTC = Tokyo shut; open ~18% of days, close ~0% — unusable). The model converges
  ADR-close → Tokyo-open, so those bars are the whole point.
- **Format:** 5-min, adjusted, ISO timestamps with tz offset, columns `datetime,open,high,low,close,volume`;
  window Jun 2025 → present; filenames `{CODE}_TSEJ_5min_adjusted.csv` (ords) / `{TICKER}_5min_adjusted.csv` (US).
- **Validate** before delivering: per ord, % of days with a bar in `00:00–00:30 UTC` (open) AND
  `05:30–06:00 UTC` (close) — both ~100%; if not, the windowing is wrong, fix it.
- **Deliver:** save CSVs locally (e.g. an `out/` folder) + write a local `pull_status.md` (instruments
  pulled, open/close coverage % per ord, date range, issues). **Do not push** — collected out-of-band.
