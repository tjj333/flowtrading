# flowtrading — cross-PC coordination

Shared channel between the **Japan Directional** machine (model/dashboard) and the **data-pull
worker** machine (IBKR / Bloomberg). Coordination artifacts only — no model code, positions, or P&L.

## The instruction model
- **`START_HERE.md` IS the worker's prompt.** It always contains the complete CURRENT task.
- The coordinating session **rewrites `START_HERE.md` in place** whenever the task changes (more data,
  more tickers, a different pull) and pushes it.
- The worker is told "new instructions" → it does `git pull` and re-reads `START_HERE.md`.
- The repo is **READ-ONLY for the worker** — it never commits/pushes; outputs are collected out-of-band.

## Files
- **`START_HERE.md`** — the living instruction prompt (point the worker here, every time).
- **`bloomberg_fields.md`** — reference: validated Bloomberg field mnemonics for the static event/corp-action pull.
