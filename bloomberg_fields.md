# Bloomberg reference fields — static event / corp-action pull

Validated against the live Terminal (//blp/refdata, ReferenceDataRequest). These are STATIC —
pull ONCE, cache, never re-poll. One batched request for the universe (99 JP ADRs + 99 Tokyo ords
+ semi peers + mega-caps). Pull JP corp-actions/meetings on the **ord** (.T, primary listing) and
earnings on the ADR/ord; US peers/mega on their US ticker.

## Use these (return data)
| item | field(s) |
|---|---|
| Earnings — next | `EXPECTED_REPORT_DT` |
| Earnings — last | `ANNOUNCEMENT_DT` |
| **Sales** — results date | `SALES_RESULT_DATE`  *(DT651 — Tom)* ; revenue figure `SALES_REV_TURN` |
| **AGM / meetings — next** | `NEXT_CORP_MEETING_DT` + `NEXT_CORP_MEETING_TYP`  *(TYP = "Annual Shareholder" identifies the AGM — Tom)* |
| AGM / meetings — last | `LAST_CORP_MEETING_DT` + `LAST_CORP_MEETING_TYP` |
| Dividends — next ex (declared) | `DVD_EX_DT` ; record `DVD_RECORD_DT` ; pay `DVD_PAY_DT` |
| Dividends — next ex (forecast, forward) | `BDVD_NEXT_EST_EX_DT` |
| Dividend frequency | `DVD_FREQ` |
| Dividend / stock-div / split HISTORY (bulk) | `DVD_HIST_ALL` — cash + stock dividends + splits, with ex-date/type/amount; future-dated rows = upcoming |
| Splits / stock dividends (bulk) | `EQY_DVD_HIST_SPLITS` |

## Do NOT use (invalid mnemonics — return fieldException)
`NEXT_ANNUAL_GENERAL_MEETING_DT`, `EXPECTED_NEXT_AGM_DATE`, `AGM_DATE`, `NEXT_AGM_DT`,
`ANNUAL_GENERAL_MEETING_DATE`, `SHAREHOLDER_MEETING_DATE`, `BDVD_PROJ_EX_DT`, `FISCAL_YEAR_END`.

## Notes
- AGM via the simple fields above (NEXT/LAST_CORP_MEETING + TYP); the dedicated AGM mnemonics don't exist.
- Most JP names (March fiscal year-end) hold the AGM late June — useful sanity check / fallback when NEXT is N.A.
- Write into the existing schemas (key column `adr`): `earnings.csv` (adr,earnings_date), `ex_div.csv`
  (adr,ex_date,amount), `splits.csv` (adr,split_date,ratio), `meetings.csv` (adr,meeting_date,type).
