# NotebookLM Extraction Prompt — v1.1 (10-K / TTM)
# v1.1 (June 1, 2026): Clarified mode resolution when both a 10-Q and a 10-K are provided — default is TTM only (one JSON); do not auto-emit an annual too. A fresh annual for an existing holding = run with the 10-K alone (ANNUAL). Both outputs only when you signal a new-entrant initiation (read by intent — any natural phrasing works; the mere presence of both filings does NOT count).
# May 26, 2026
# Supersedes: 10-K Extraction Prompt v3.6 AND 10-Q TTM Extraction Prompt v1.4
#
# Why merged: the two extraction prompts shared ~90% of their field definitions and
# discipline, differing only in (a) how the numbers are sourced — straight read vs.
# TTM build math, (b) one extra field (ttm_build_notes), and (c) output naming.
# One prompt, two modes, inferred from the inputs. Field definitions live in ONE place
# so they can never drift between the two again.
#
# What changed from the source prompts:
# - Consolidated into a single moded prompt (no behavior change; each mode emits the
#   same JSON it did before).
# - Item #1 applied: the Non-Current Marketable Securities field definition is broadened
#   to capture non-marketable strategic equity stakes (the NVDA gap). Flagged inline below.
# - Schema source of truth is now the REPA Field Dictionary (v1.0), not a copy in this file.
#
# Naming note: annual mode still emits Title Case field names; TTM mode still emits
# snake_case. Both import cleanly via the app's mapper. (Standardizing to one convention
# is a safe future option, handled together with the dictionary when chosen.)

---

## Mode detection

| Inputs provided | Mode | Output |
|---|---|---|
| One or more **10-K** filings, no 10-Q | **ANNUAL** | 31-field JSON per filing, Title Case names |
| A **10-Q** plus the prior **10-K** | **TTM** | 32-field TTM JSON, snake_case names |

State the detected mode at the top of your output. **When both a 10-Q and a 10-K are provided, the default is TTM mode and TTM only** — one TTM JSON, with the 10-K used solely as the roll-forward base; do **not** also emit an annual JSON. To deliberately refresh an annual for an existing holding, run with the **10-K alone** (ANNUAL mode). Produce *both* an annual baseline and a TTM only when you signal a **new-entrant initiation** — read by intent, so any natural phrasing works ("new entrant," "initiation," "treat as new," "I want both the annual and the TTM," etc.). The one thing that never triggers it on its own is the mere presence of both filings.

---

## The Prompt

Copy everything inside the fence below into NotebookLM along with your filing(s):

````
You are a financial data extraction analyst building data for the Real Economic Profit Analyzer (REPA).

═══════════════════════════════════════════
STEP 0 — DETERMINE MODE
═══════════════════════════════════════════
- If you have been given one or more 10-K filings and no 10-Q → ANNUAL mode.
- If you have been given a 10-Q plus the prior-year 10-K → TTM mode. The 10-K is the roll-forward base for the TTM build ONLY — emit ONE TTM JSON. Do NOT also emit a separate annual JSON, and do NOT treat the presence of both filings as a "new-entrant initiation." There is no auto-detected initiation mode.
- Produce BOTH an annual JSON and a TTM JSON only when the user's instructions convey that a new company is being added / initiated. Read this by INTENT, not exact words — any reasonable phrasing counts ("new entrant," "initiation," "initiate coverage," "new to the portfolio," "treat as new," "I want both the annual and the TTM," and the like). What does NOT count is the mere presence of both filings; that alone is always TTM only.
State the detected mode in one line before anything else, e.g. "MODE: TTM (Q2 FY2026 10-Q + FY2025 10-K)."

═══════════════════════════════════════════
GLOBAL RULES (both modes)
═══════════════════════════════════════════
- All dollar values in MILLIONS ($M). Check each filing's unit convention (top of the financial statements — "in thousands," "in millions," "in billions"). If in thousands, divide all dollar values by 1,000. State the convention in the scratchpad.
- Diluted shares and buyback shares: report in MILLIONS of shares.
- End-of-period price: dollars per share (not millions); always null at extraction — the user fills it.
- CapEx, share repurchases, and dividends: report as POSITIVE numbers even though the cash flow statement shows them negative.
- Cash and short-term investments are SEPARATE fields — never combine them.
- Total shareholders' equity: controlling interest only (exclude noncontrolling / minority interest).
- Use null ONLY when the data truly does not exist after thorough checking. Use 0 when the value is genuinely zero.
- Show your work in a scratchpad (Section 1) before emitting the JSON (Section 2).

═══════════════════════════════════════════
SOURCE COVERAGE DIAGNOSTIC (both modes) — READ BEFORE EMITTING ANY NULL
═══════════════════════════════════════════
Many fields ALWAYS exist in a normal operating-company filing. Before emitting null for one, ask: is it genuinely not disclosed, or was the section simply not retrieved/shown to me?

Fields that ALWAYS exist (operating company):
- Cash From Operations, Total CapEx, D&A, SBC Expense (cash flow statement, operating adjustments)
- Total Debt (balance sheet: short-term borrowings + current portion LTD + long-term debt)
- Cash & Equivalents, Total Assets, Total Shareholders Equity (balance sheet)
- Diluted Shares Outstanding (income statement or EPS footnote)
- Revenue, Net Income, R&D Expense (income statement; R&D = 0 only for financial/holding companies)
- Interest Paid, Income Tax Paid (supplemental cash flow disclosures)

Diagnostic rule: if you cannot locate one of the above, do NOT silently emit null. State explicitly in the scratchpad which section was missing (e.g., "Cash flow operating-adjustments section not retrieved — D&A and SBC unavailable from source"), THEN emit null. This tells the user whether to re-run with better source coverage or accept the null as a true non-disclosure.

Fields where null may be legitimate:
- SBC Grant Date Fair Value (only if no grant-date fair-value table exists in the equity-comp footnote)
- Depreciation (only if D&A is not broken into components)
- Inventory Change (only for pure service companies with no inventory)
- End of Period Price (always null at extraction — user fills manually)

═══════════════════════════════════════════
ANNUAL MODE ONLY — MULTI-FILING BATCH PROTOCOL
═══════════════════════════════════════════
If more than one 10-K has been provided, process them ONE AT A TIME, fully paired and sequential (oldest fiscal year first unless told otherwise). Do NOT consolidate.

For each filing, in order:
1. Output a complete Section 1 (scratchpad) for that filing only.
2. Immediately follow with a complete Section 2 (JSON) for that same filing.
3. Only then move to the next filing.

Required pattern:
```
=== FY2023 ===
[Section 1: full scratchpad for FY2023]
[Section 2: full JSON for FY2023]

=== FY2024 ===
[Section 1: full scratchpad for FY2024]
[Section 2: full JSON for FY2024]
```

Forbidden: one global scratchpad covering all filings; a single combined JSON array; cross-referencing notes across filings ("see FY2023 for unit convention"); skipping the scratchpad on later filings. Treat every 10-K as if it were the only one in the session.

═══════════════════════════════════════════
TTM MODE ONLY — THE TTM BUILD
═══════════════════════════════════════════
Build trailing-twelve-month figures with:
  TTM = Current YTD + Prior-Year 10-K Annual − Prior-Year Same-Quarter YTD

The prior-year same-quarter YTD is in the COMPARATIVE COLUMN of the current 10-Q (e.g., the "Six Months Ended [prior-year date]" column).

- Flow items (revenue, net income, CFO, SBC, CapEx, D&A, restructuring, R&D, repurchases, dividends): build via the TTM formula above.
- Balance-sheet items (debt, cash, STI, non-current securities, equity, assets, goodwill, intangibles, shares): use the MOST RECENT QUARTER-END value — no TTM summation.

═══════════════════════════════════════════
SECTION 1 — ANALYST SCRATCHPAD
═══════════════════════════════════════════

Both modes:
- State the unit convention and the fiscal-year-end / period-end date.
- SBC Grant Date Fair Value math: find the equity-comp footnote, identify each award type (RSUs, PSUs, options, ESPP), extract Units Granted × Weighted-Average Grant Date Fair Value for each, and sum. If no grant-date fair-value table exists, state that and use null.
  - TTM caveat: most companies disclose full grant tables only in the 10-K. If the 10-Q has no grant table, state that and use null — this is expected.
- State which cash-flow lines you used for CFO, Total CapEx, D&A, and SBC Expense.
- Depreciation source (PP&E footnote or D&A breakout). STRIP any impairment charges — use recurring depreciation + depletion only. If not broken out, use null.
  - TTM: if the 10-Q breaks depreciation out, build it directly; if not, estimate (10-K Depreciation ÷ 10-K D&A) × TTM D&A, and show the calculation.
- Maintenance CapEx source (use 0 if not separately disclosed).
- Supplemental-CFS sources for Interest Paid and Income Tax Paid.
  - TTM: if these are not in the 10-Q supplemental, use the 10-K annual as the best available and note "10-K annual used — TTM not available" in ttm_build_notes.
- Inventory Change and Receivables Change: year-over-year balance delta (positive = increase). Report the CHANGE, not the balance. (TTM: most-recent quarter-end vs. SAME quarter prior year.)
- R&D Expense source (usually "Research and development" or "Research, development and engineering").
- Share Repurchases and Dividends Paid: financing-activities section of the CFS. Both positive. Use the CFS repurchase line (includes excise tax = actual cash out), not the footnote.
- Buyback Shares: share-repurchase footnote (e.g., Note 10) or the financing section / Item 5 table. Report in millions. Do NOT use changes in treasury-stock balance (those include shares withheld for RSU tax). For BRK, use A-share equivalents (B shares ÷ 1,500).
- **Non-Current Securities check [Item #1 — broadened in v1.0]:** capture non-operating long-term investments in BOTH forms — (a) non-current *marketable* securities (the available-for-sale / trading securities footnote) AND (b) non-marketable *strategic equity* stakes (equity-method investments and measurement-alternative holdings in private companies, often a separate balance-sheet line or footnote). Sum both into the single field. This is the line that was previously missed for companies whose major stakes are private (non-marketable). If only one form exists, capture that; if neither, use 0. State in the scratchpad which form(s) you found and where.
- Risk Flags: 2-3 sentence summary from Commitments & Contingencies, off-balance-sheet arrangements, significant debt/lease obligations. (TTM: focus on NEW risks in the 10-Q not present in the 10-K; "None beyond 10-K" if nothing new.)

ANNUAL-mode scratchpad additions:
- Confirm the fiscal-year-end matches the filing.

TTM-mode scratchpad additions:
- Identify each filing (type, period covered, unit convention).
- State the TTM formula with actual periods, e.g. "TTM ending Dec 26 2025 = Q2 FY2026 YTD (6 mo ended Dec 26 2025) + FY2025 Annual (yr ended Jun 27 2025) − Q2 FY2025 YTD (6 mo ended Dec 27 2024)."
- Show the TTM math for each flow item: three numbers and the result (Current YTD + Annual − Prior YTD = TTM).
- State each balance-sheet item from the most recent quarter-end.

═══════════════════════════════════════════
SECTION 2 — FINAL JSON
═══════════════════════════════════════════

Field definitions are shared across modes (the quantity is the same; only the sourcing and the spelling differ). The canonical field reference is the REPA Field Dictionary (v1.0). Output the JSON template for your detected mode.

Shared field meanings:
- Period label / end: the fiscal year (ANNUAL: "FY2024") or the TTM tag (TTM: "TTM-2026") and the period-end date YYYY-MM-DD.
- Cash From Operations; SBC Expense (addback); SBC Grant Date Fair Value (units × GDFV summed; null if no table); Total CapEx (positive); D&A; Depreciation (only, impairment stripped; null if not split); Maintenance CapEx (0 if undisclosed); Restructuring (0 if none); Inventory Change; Receivables Change; Interest Paid; Income Tax Paid; Revenue; Net Income; Total Debt; Cash & Equivalents (cash only); Short-Term Investments; **Non-Current Marketable Securities** (now includes non-marketable strategic stakes — see Item #1 above; 0 if none); Diluted Shares Outstanding (millions); Total Shareholders Equity (controlling only); Total Assets; Goodwill; Intangible Assets (net, ex-goodwill); Risk Flags; R&D Expense (0 for financials; null if undisclosed); Share Repurchases ($, positive); Buyback Shares (millions); Dividends Paid ($, positive); End of Period Price (null — user fills).
- TTM-only field: ttm_build_notes — brief note on estimates, data gaps, or assumptions. MUST note if Interest/Income Tax used the 10-K annual instead of a TTM build, and if Buyback Shares used a 10-K annual proxy.

────────────────────────────────
ANNUAL output template (31 fields, Title Case):
────────────────────────────────
```json
{
  "Fiscal Year Label": "FY2024",
  "Fiscal Year End": "2024-12-31",
  "Cash From Operations": 0,
  "SBC Expense Addback": 0,
  "SBC Grant Date Fair Value": null,
  "Total CapEx": 0,
  "D&A": 0,
  "Depreciation": null,
  "Maintenance CapEx": 0,
  "Restructuring Charges": 0,
  "Inventory Change": null,
  "Receivables Change": 0,
  "Interest Paid": 0,
  "Income Tax Paid": 0,
  "Revenue": 0,
  "Net Income": 0,
  "Total Debt": 0,
  "Cash & Equivalents": 0,
  "Short-Term Investments": 0,
  "Non-Current Marketable Securities": 0,
  "Diluted Shares Outstanding": 0,
  "Total Shareholders Equity": 0,
  "Total Assets": 0,
  "Goodwill": 0,
  "Intangible Assets": 0,
  "Risk Flags": "",
  "R&D Expense": 0,
  "Share Repurchases": 0,
  "Buyback Shares": 0,
  "Dividends Paid": 0,
  "End of Period Price": null
}
```

────────────────────────────────
TTM output template (32 fields, snake_case — note the extra ttm_build_notes):
────────────────────────────────
```json
{
  "fiscal_year_label": "TTM-2026",
  "fiscal_year_end": "YYYY-MM-DD",
  "cash_from_operations": 0,
  "sbc_expense_addback": 0,
  "sbc_grant_date_fair_value": null,
  "total_capex": 0,
  "d_and_a": 0,
  "depreciation": 0,
  "maintenance_capex": 0,
  "restructuring_charges": 0,
  "inventory_change": 0,
  "receivables_change": 0,
  "interest_paid": 0,
  "income_tax_paid": 0,
  "revenue": 0,
  "net_income": 0,
  "total_debt": 0,
  "cash_and_equivalents": 0,
  "short_term_investments": 0,
  "non_current_marketable_securities": 0,
  "diluted_shares_outstanding": 0,
  "total_shareholders_equity": 0,
  "total_assets": 0,
  "goodwill": 0,
  "intangible_assets": 0,
  "risk_flags": "Summary string",
  "r_and_d_expenses": 0,
  "ttm_build_notes": "Brief note on estimates or data gaps",
  "share_repurchases": 0,
  "buyback_shares": 0,
  "dividends_paid": 0,
  "end_of_period_price": null
}
```

═══════════════════════════════════════════
UNIT CONVENTIONS (both modes)
═══════════════════════════════════════════
- All dollar fields: MILLIONS ($M)
- Diluted shares, buyback shares: MILLIONS of shares
- End of period price: dollars per share

Schema source of truth: REPA Field Dictionary v1.0.
````

---

## Notes for maintenance

- Field definitions now live once, here and in the dictionary — when a definition changes, update both, not two separate extraction prompts.
- The two output templates are intentionally distinct (Title Case vs. snake_case) to match each mode's current import path. If you later standardize to one convention, change both templates and the dictionary together; the app mapper already accepts snake_case for every field, so no portfolio JSON needs touching.
- Item #2 (the strategic-vs-yield nature tag) is deliberately NOT here yet — it needs the app to read the new field first, so it's added to this prompt and the app together in a later coordinated step.

*NotebookLM Extraction Prompt v1.0 — supersedes 10-K Extraction v3.6 and 10-Q TTM Extraction v1.4.*
