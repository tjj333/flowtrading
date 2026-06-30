# START HERE — instruction prompt for the data-pull worker

**This file IS your prompt.** Whenever you're told "new instructions", `git pull` and re-read THIS
file top to bottom — it is rewritten in place when the task changes. Everything you need is here.

## Operating model
- Pull this repo → read THIS file → execute it **exactly** → save outputs **LOCALLY**.
- This repo is **READ-ONLY for you: NEVER commit or push anything back.**
- The owner collects your output **out-of-band** (not via git).
- You have **IBKR** (and Bloomberg) data access. `bloomberg_fields.md` in this repo is reference only.

## Connect
```
git clone https://github.com/tjj333/flowtrading      # or, if already cloned:  cd flowtrading && git pull
```

---

# CURRENT TASK — IBKR historical 5-minute pull (to unblock the out-of-sample backtest)

The model's data tree currently holds only 5 ADRs + 2 futures + FX. We need the other legs over the
IBKR window (~Jun 2025 → Apr 2026, which pre-dates the tuning period, so it's a genuine OOS test).

## Already have — DO NOT re-pull
- ADRs (5): KXIAY, SFTBY, TOELY, ATEYY, MRAAY
- Futures: NKD_F (NK1 / Nikkei 225), ES_F (ES1) ; FX: USDJPY

## Shared spec (every instrument)
- 5-minute bars, **ADJUSTED** (splits + dividends)
- ISO-8601 timestamps with tz offset (e.g. 2025-10-06T09:35:00-04:00); columns: `datetime,open,high,low,close,volume`
- Window: Jun 2025 → present (or each name's listing start)
- US-listed (ADRs, ETFs): US cash session 09:30–16:00 ET, SMART routing
- Tokyo ords (.T): **TSE REGULAR CASH SESSION 09:00–11:30 + 12:30–15:00 JST** (= 00:00–02:30 + 03:30–06:00 UTC).
  Every trading day **MUST** include the **OPEN bars (09:00–09:30 JST = 00:00–00:30 UTC)** and **CLOSE bars
  (14:30–15:00 JST = 05:30–06:00 UTC)**. Full session — **NOT "firstlast"**. (The earlier *_TSEJ_firstlast_*
  files were mis-windowed: bars at 14:00–16:00 UTC = Tokyo shut; open present ~18% of days, close ~0% — unusable.)
  The model converges ADR-close → Tokyo-open, so the open/close bars are the entire point.
- Filenames: US-listed = `{TICKER}_5min_adjusted.csv` ; Tokyo ords = `{CODE}_TSEJ_5min_adjusted.csv`

## TIER 1 — DO FIRST (the core book, ~85% of strategy P&L)
Tokyo ords: `285A.T` (KXIAY)  `9984.T` (SFTBY)  `8035.T` (TOELY)  `6857.T` (ATEYY)  `6981.T` (MRAAY)  `5803.T` (FJIKY)
ADR still missing: `FJIKY`
Benchmarks (US RTH, adjusted): `SMH`, `ARM`  (SMH = semis confirm; ARM = SoftBank blend; NKD already present)
`5803.T` (Fujikura) may be limit-up for stretches — pull anyway, flag if sparse/zero-volume.

## TIER 2 — full 99-name universe (lower priority; the edge is concentrated, so this mainly confirms breadth dilutes)
93 missing ADRs (US RTH, adjusted):
DSCSY MHVIY MPJPY SMTOY NRILY NTTYY SMCAY TTDKY DKILY RNECY MTSUY IPXHY IHICY KWHIY MSLOY SKHSY SSDOY JPPTY TMICY OLCLY DLICY KAOOY SOBKY EBCOY MSADY JAPSY DQJCY MTSFY SMNNY CCOEY NDEKY NCBDY EJPRY ASCCY SMPNY PCRHY JPXGY FUJHY TRUMY AONNY RSHGY OLYMY NTDOY LSRCY SMMYY RCRUY FRCOY SHECY HTHIY TKOMY SONY SMFG MFG TM HMC MUFG SUTNY NPSCY FUJIY OTSKY SZKMY MIELY DFKCY CHGCY MITEY BRDCY MARUY JAPAY KDDIY ITOCY DNZOY SVNDY FJTSY FANUY KUBTY TOTDY MITSY MTHRY SSUMY DSNKY KMTUY HOCPY AJNMY CAJPY NCLTY SOMLY CJPRY IX TAK ESAIY ISUZY OBICY OMRNY

93 missing Tokyo ords — `ord` (ADR):
`6146.T`(DSCSY) `7011.T`(MHVIY) `3350.T`(MPJPY) `5802.T`(SMTOY) `4307.T`(NRILY) `9432.T`(NTTYY) `6273.T`(SMCAY) `6762.T`(TTDKY) `6367.T`(DKILY) `6723.T`(RNECY) `8058.T`(MTSUY) `1605.T`(IPXHY) `7013.T`(IHICY) `7012.T`(KWHIY) `9104.T`(MSLOY) `1928.T`(SKHSY) `4911.T`(SSDOY) `7182.T`(JPPTY) `4704.T`(TMICY) `4661.T`(OLCLY) `8750.T`(DLICY) `4452.T`(KAOOY) `9434.T`(SOBKY) `6361.T`(EBCOY) `8725.T`(MSADY) `9201.T`(JAPSY) `7532.T`(DQJCY) `8801.T`(MTSFY) `7309.T`(SMNNY) `9697.T`(CCOEY) `6988.T`(NDEKY) `7832.T`(NCBDY) `9020.T`(EJPRY) `7936.T`(ASCCY) `8630.T`(SMPNY) `6752.T`(PCRHY) `8697.T`(JPXGY) `7270.T`(FUJHY) `4543.T`(TRUMY) `8267.T`(AONNY) `8308.T`(RSHGY) `7733.T`(OLYMY) `7974.T`(NTDOY) `6920.T`(LSRCY) `5713.T`(SMMYY) `6098.T`(RCRUY) `9983.T`(FRCOY) `4063.T`(SHECY) `6501.T`(HTHIY) `8766.T`(TKOMY) `6758.T`(SONY) `8316.T`(SMFG) `8411.T`(MFG) `7203.T`(TM) `7267.T`(HMC) `8306.T`(MUFG) `8309.T`(SUTNY) `5401.T`(NPSCY) `4901.T`(FUJIY) `4578.T`(OTSKY) `7269.T`(SZKMY) `6503.T`(MIELY) `6383.T`(DFKCY) `4519.T`(CHGCY) `8802.T`(MITEY) `5108.T`(BRDCY) `8002.T`(MARUY) `2914.T`(JAPAY) `9433.T`(KDDIY) `8001.T`(ITOCY) `6902.T`(DNZOY) `3382.T`(SVNDY) `6702.T`(FJTSY) `6954.T`(FANUY) `6326.T`(KUBTY) `5332.T`(TOTDY) `8031.T`(MITSY) `2413.T`(MTHRY) `8053.T`(SSUMY) `4568.T`(DSNKY) `6301.T`(KMTUY) `7741.T`(HOCPY) `2802.T`(AJNMY) `7751.T`(CAJPY) `9843.T`(NCLTY) `9735.T`(SOMLY) `9022.T`(CJPRY) `8591.T`(IX) `4502.T`(TAK) `4523.T`(ESAIY) `7202.T`(ISUZY) `4684.T`(OBICY) `6645.T`(OMRNY)

## Validate — before delivering
For each ord: % of trading days with ≥1 bar in `00:00–00:30 UTC` (open) AND ≥1 bar in `05:30–06:00 UTC`
(close). Both should be ~100%. If either is low the windowing is still wrong — **fix it, do not deliver.**

## Deliver
- Save the CSVs locally (e.g. an `out/` folder) with the filenames in the Shared spec.
- Write a local `pull_status.md`: instruments pulled, open/close coverage % per ord, date range per file, any issues.
- **Do NOT push** — the data + status are collected from this machine out-of-band.
