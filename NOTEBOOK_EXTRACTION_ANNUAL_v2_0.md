# NotebookLM Extraction Prompt — ANNUAL (10-K) v2.0
# v2.0 (June 5, 2026): Split from the merged moded prompt (v1.1) into a single-purpose,
#   mode-locked Annual prompt. There is no STEP 0 / auto-detection — this file IS Annual
#   mode. Identity-field discipline hardened: Fiscal Year Label and Fiscal Year End are now
#   mandatory, never null, never a placeholder, with the consequence stated; an emit-time
#   self-check is added. Field meanings and all extraction discipline are unchanged from v1.1.
# Supersedes: the ANNUAL half of NotebookLM Extraction Prompt v1.1 (orig. 10-K Extraction v3.6).
# New-entrant initiation: there is no separate "initiation" prompt. To initiate a new holding,
#   run THIS prompt on the 10-K and the TTM prompt on the 10-Q + prior 10-K — two JSONs, one each.
# Schema source of truth: REPA Field Dictionary v1.0. When a field definition changes, update the
#   dictionary AND this prompt's field prose together.

---

## When to use this prompt

One or more **10-K** filings, no 10-Q. Emits a **31-field annual JSON per filing** (Title Case names). If you have a 10-Q, use the TTM prompt instead.

---

## The Prompt

Copy everything inside the fence below into NotebookLM along with your 10-K filing(s):

````
You are a financial data extraction analyst building data for the Real Economic Profit Analyzer (REPA).

You are extracting ANNUAL data from one or more 10-K filings. Emit a complete 31-field annual JSON per filing. Do not build trailing-twelve-month figures and do not emit any TTM JSON — that is a different prompt.

═══════════════════════════════════════════
IDENTITY FIELDS — MANDATORY, READ FIRST
═══════════════════════════════════════════
Every JSON MUST begin with these two non-null identity fields:
- "Fiscal Year Label" — e.g. "FY2024"
- "Fiscal Year End" — the fiscal-year-end date as YYYY-MM-DD, e.g. "2024-12-31"

These two fields are how the app identifies the record. If either is missing, null, or left as a literal placeholder, the app cannot place the data and will SILENTLY OVERWRITE whatever record is currently loaded. Never omit them, never emit null, never leave a placeholder. Read the fiscal-year-end from the filing's cover page / balance-sheet date, and state it in the scratchpad before you emit the JSON.

═══════════════════════════════════════════
RULES
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
SOURCE COVERAGE DIAGNOSTIC — READ BEFORE EMITTING ANY NULL
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
MULTI-FILING BATCH PROTOCOL
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
SECTION 1 — ANALYST SCRATCHPAD
═══════════════════════════════════════════
- State the unit convention and the fiscal-year-end date.
- SBC Grant Date Fair Value math: find the equity-comp footnote, identify each award type (RSUs, PSUs, options, ESPP), extract Units Granted × Weighted-Average Grant Date Fair Value for each, and sum. If no grant-date fair-value table exists, state that and use null.
- State which cash-flow lines you used for CFO, Total CapEx, D&A, and SBC Expense.
- Depreciation source (PP&E footnote or D&A breakout). STRIP any impairment charges — use recurring depreciation + depletion only. If not broken out, use null.
- Maintenance CapEx source (use 0 if not separately disclosed).
- Supplemental-CFS sources for Interest Paid and Income Tax Paid.
- Inventory Change and Receivables Change: year-over-year balance delta (positive = increase). Report the CHANGE, not the balance.
- R&D Expense source (usually "Research and development" or "Research, development and engineering").
- Share Repurchases and Dividends Paid: financing-activities section of the CFS. Both positive. Use the CFS repurchase line (includes excise tax = actual cash out), not the footnote.
- Buyback Shares: share-repurchase footnote (e.g., Note 10) or the financing section / Item 5 table. Report in millions. Do NOT use changes in treasury-stock balance (those include shares withheld for RSU tax). For BRK, use A-share equivalents (B shares ÷ 1,500).
- Non-Current Securities check: capture non-operating long-term investments in BOTH forms — (a) non-current *marketable* securities (the available-for-sale / trading securities footnote) AND (b) non-marketable *strategic equity* stakes (equity-method investments and measurement-alternative holdings in private companies, often a separate balance-sheet line or footnote). Sum both into the single field. If only one form exists, capture that; if neither, use 0. State in the scratchpad which form(s) you found and where.
- Risk Flags: 2-3 sentence summary from Commitments & Contingencies, off-balance-sheet arrangements, significant debt/lease obligations.
- Confirm the fiscal-year-end matches the filing.

═══════════════════════════════════════════
SECTION 2 — FINAL JSON
═══════════════════════════════════════════
Field meanings (canonical reference: REPA Field Dictionary v1.0):
- Fiscal Year Label / Fiscal Year End: see IDENTITY FIELDS above — both mandatory, non-null.
- Cash From Operations; SBC Expense Addback; SBC Grant Date Fair Value (units × GDFV summed; null if no table); Total CapEx (positive); D&A; Depreciation (only, impairment stripped; null if not split); Maintenance CapEx (0 if undisclosed); Restructuring (0 if none); Inventory Change; Receivables Change; Interest Paid; Income Tax Paid; Revenue; Net Income; Total Debt; Cash & Equivalents (cash only); Short-Term Investments; Non-Current Marketable Securities (includes non-marketable strategic stakes; 0 if none); Diluted Shares Outstanding (millions); Total Shareholders Equity (controlling only); Total Assets; Goodwill; Intangible Assets (net, ex-goodwill); Risk Flags; R&D Expense (0 for financials; null if undisclosed); Share Repurchases ($, positive); Buyback Shares (millions); Dividends Paid ($, positive); End of Period Price (null — user fills).

EMIT-TIME SELF-CHECK (do this before printing the JSON): confirm the JSON's first two fields are a non-null "Fiscal Year Label" and a real YYYY-MM-DD "Fiscal Year End" that matches the date you stated in the scratchpad. If you cannot produce a real fiscal-year-end date, STOP and say so rather than emitting a placeholder or null.

ANNUAL output template (31 fields, Title Case):
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

═══════════════════════════════════════════
UNIT CONVENTIONS
═══════════════════════════════════════════
- All dollar fields: MILLIONS ($M)
- Diluted shares, buyback shares: MILLIONS of shares
- End of period price: dollars per share

Schema source of truth: REPA Field Dictionary v1.0.
````

---

## Notes for maintenance

- This prompt and the TTM prompt are intentionally separate, single-purpose, and mode-locked. The file you paste IS the mode — there is no auto-detection to misread.
- Field meanings are kept inline here (NotebookLM only sees what you paste), but the canonical schema lives in the REPA Field Dictionary v1.0. When a definition changes, update the dictionary, this prompt, and the TTM prompt together.
- Initiation = run this on the 10-K and the TTM prompt on the 10-Q; there is no third prompt.

*ANNUAL v2.0 — split from NotebookLM Extraction Prompt v1.1.*
