# jp-flow — cross-PC coordination

Shared channel between the **Japan Directional** machine (model/dashboard) and the **IBKR-pull
machine**. Coordination artifacts only — no model code, positions, or P&L live here.

## Contents
- **`ibkr_oos_pull.md`** — the precise IBKR data request to complete the out-of-sample backtest
  (Tier 1 = core book, do first; Tier 2 = full 99-name universe). The IBKR PC should read this and
  reply (commit a `pull_status.md`) with what was pulled + the open/close-bar coverage per ord.
- **`bloomberg_fields.md`** — validated Bloomberg reference-data field mnemonics for the static
  event/corp-action pull (earnings, sales, AGM/meetings, dividends, splits, stock dividends).

## Workflow
1. IBKR PC: `git pull`, read `ibkr_oos_pull.md`, run the pull, write a short `pull_status.md` here, `git push`.
2. Japan Directional PC: `git pull` to pick up status / delivered-file manifests.

(Bulk price CSVs are NOT committed here — too large for git. They move via the IBKR data tree;
this repo carries the specs, field lists, and status notes that keep the two machines in sync.)
