# jp-flow — cross-PC coordination

Shared channel between the **Japan Directional** machine (model/dashboard) and the **IBKR-pull
machine**. Coordination artifacts only — no model code, positions, or P&L live here.

## Contents
- **`ibkr_oos_pull.md`** — the precise IBKR data request to complete the out-of-sample backtest
  (Tier 1 = core book, do first; Tier 2 = full 99-name universe). The IBKR PC should read this and
  reply (commit a `pull_status.md`) with what was pulled + the open/close-bar coverage per ord.
- **`bloomberg_fields.md`** — validated Bloomberg reference-data field mnemonics for the static
  event/corp-action pull (earnings, sales, AGM/meetings, dividends, splits, stock dividends).

## Workflow (this repo is READ-ONLY for the worker machines)
The coordinating session publishes instruction `.md` files here. Worker machines (IBKR / Bloomberg)
**only read** them — they do NOT push anything back.
1. Worker PC: `git pull`, read the relevant instruction file (e.g. `ibkr_oos_pull.md`), execute it,
   save outputs **locally**, write a local `pull_status.md`.
2. The owner collects the data + status from the worker machine **out-of-band** (not via git).

(No data is committed here — instructions, field lists, and specs only.)
