# NotebookLM Extraction Prompt — TTM (10-Q) v2.0
# v2.0 (June 5, 2026): Split from the merged moded prompt (v1.1) into a single-purpose,
#   mode-locked TTM prompt. There is no STEP 0 / auto-detection — this file IS TTM mode and
#   ALWAYS emits exactly ONE TTM JSON, never an annual one. Identity-field discipline hardened:
#   fiscal_year_end is now shown as a real most-recent-quarter-end date (no "YYYY-MM-DD"
#   placeholder), both identity fields are mandatory and never null, and an emit-time self-check
#   is added. The TTM build math and all field meanings are unchanged from v1.1.
# Supersedes: the TTM half of NotebookLM Extraction Prompt v1.1 (orig. 10-Q TTM Extraction v1.4).
# Inputs: a 10-Q PLUS the prior-year 10-K. The 10-K is the roll-forward base for the TTM math ONLY.
# New-entrant initiation: also run the Annual prompt on the 10-K. This TTM prompt still emits only
#   ONE TTM JSON regardless.
# Schema source of truth: REPA Field Dictionary v1.0. When a field definition changes, update the
#   dictionary AND this prompt's field prose together.

---

## When to use this prompt

A **10-Q** plus the **prior-year 10-K**. Emits exactly **one 32-field TTM JSON** (snake_case names; includes the extra `ttm_build_notes`). For a straight annual read, use the Annual prompt instead.

---

## The Prompt

Copy everything inside the fence below into NotebookLM along with your 10-Q and the prior-year 10-K:

````
You are a financial data extraction analyst building data for the Real Economic Profit Analyzer (REPA).

You are building ONE trailing-twelve-month (TTM) JSON from a 10-Q plus the prior-year 10-K. The 10-K is the roll-forward base for the TTM math ONLY. Emit exactly one TTM JSON. Do NOT also emit an annual JSON.

═══════════════════════════════════════════
IDENTITY FIELDS — MANDATORY, READ FIRST
═══════════════════════════════════════════
Every JSON MUST begin with these two non-null identity fields:
- "fiscal_year_label" — the TTM tag, e.g. "TTM-2026" (or "TTM 2026-Q1")
- "fiscal_year_end" — the MOST-RECENT QUARTER-END date as YYYY-MM-DD, e.g. "2026-03-31". This is a real date you read from the 10-Q balance sheet. NEVER emit the literal text "YYYY-MM-DD", and NEVER null.

These two fields are how the app identifies the record. The fiscal_year_end (the most-recent quarter-end) is specifically what keeps this TTM record SEPARATE from the prior annual record. If it is missing, null, or a placeholder, the app cannot place the data and will SILENTLY OVERWRITE whatever record is currently loaded (typically the annual pill you built the TTM from). Read the quarter-end date from the 10-Q and state it in the scratchpad before you emit the JSON.

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
- SBC Grant Date Fair Value (only if no grant-date fair-value table exists in the equity-comp footnote; most companies disclose full grant tables only in the 10-K, so a 10-Q with no grant table → null is expected)
- Depreciation (only if D&A is not broken into components)
- Inventory Change (only for pure service companies with no inventory)
- End of Period Price (always null at extraction — user fills manually)

═══════════════════════════════════════════
THE TTM BUILD
═══════════════════════════════════════════
Build trailing-twelve-month figures with:
  TTM = Current YTD + Prior-Year 10-K Annual − Prior-Year Same-Quarter YTD

The prior-year same-quarter YTD is in the COMPARATIVE COLUMN of the current 10-Q (e.g., the "Six Months Ended [prior-year date]" column).

- Flow items (revenue, net income, CFO, SBC, CapEx, D&A, restructuring, R&D, repurchases, dividends): build via the TTM formula above.
- Balance-sheet items (debt, cash, STI, non-current securities, equity, assets, goodwill, intangibles, shares): use the MOST RECENT QUARTER-END value — no TTM summation.

═══════════════════════════════════════════
SECTION 1 — ANALYST SCRATCHPAD
═══════════════════════════════════════════
- State the unit convention and the period-end (most-recent quarter-end) date.
- Identify each filing (type, period covered, unit convention).
- State the TTM formula with actual periods, e.g. "TTM ending Dec 26 2025 = Q2 FY2026 YTD (6 mo ended Dec 26 2025) + FY2025 Annual (yr ended Jun 27 2025) − Q2 FY2025 YTD (6 mo ended Dec 27 2024)."
- Show the TTM math for each flow item: three numbers and the result (Current YTD + Annual − Prior YTD = TTM).
- State each balance-sheet item from the most recent quarter-end.
- SBC Grant Date Fair Value math: find the equity-comp footnote, identify each award type (RSUs, PSUs, options, ESPP), extract Units Granted × Weighted-Average Grant Date Fair Value for each, and sum. Most companies disclose full grant tables only in the 10-K — if the 10-Q has no grant table, state that and use null (expected).
- State which cash-flow lines you used for CFO, Total CapEx, D&A, and SBC Expense.
- Depreciation source. STRIP any impairment charges — recurring depreciation + depletion only. If the 10-Q breaks depreciation out, build it directly; if not, estimate (10-K Depreciation ÷ 10-K D&A) × TTM D&A and show the calculation. If neither is available, null.
- Maintenance CapEx source (use 0 if not separately disclosed).
- Supplemental-CFS sources for Interest Paid and Income Tax Paid. If not in the 10-Q supplemental, use the 10-K annual as the best available and note "10-K annual used — TTM not available" in ttm_build_notes.
- Inventory Change and Receivables Change: report the CHANGE, not the balance — most-recent quarter-end vs. SAME quarter prior year (positive = increase).
- R&D Expense source (usually "Research and development" or "Research, development and engineering").
- Share Repurchases and Dividends Paid: financing-activities section of the CFS. Both positive. Use the CFS repurchase line (includes excise tax = actual cash out), not the footnote.
- Buyback Shares: share-repurchase footnote or the financing section / Item 5 table. Report in millions. Do NOT use changes in treasury-stock balance (those include shares withheld for RSU tax). For BRK, use A-share equivalents (B shares ÷ 1,500). If only a 10-K annual proxy is available, note it in ttm_build_notes.
- Non-Current Securities check: capture non-operating long-term investments in BOTH forms — (a) non-current *marketable* securities (available-for-sale / trading securities footnote) AND (b) non-marketable *strategic equity* stakes (equity-method investments and measurement-alternative holdings in private companies). Sum both into the single field. If only one form exists, capture that; if neither, use 0. State which form(s) you found and where.
- Risk Flags: 2-3 sentences. Focus on NEW risks in the 10-Q not present in the 10-K; "None beyond 10-K" if nothing new.

═══════════════════════════════════════════
SECTION 2 — FINAL JSON
═══════════════════════════════════════════
Field meanings (canonical reference: REPA Field Dictionary v1.0):
- fiscal_year_label / fiscal_year_end: see IDENTITY FIELDS above — both mandatory, non-null; fiscal_year_end is the real most-recent quarter-end date.
- cash_from_operations; sbc_expense_addback; sbc_grant_date_fair_value (units × GDFV summed; null if no table); total_capex (positive); d_and_a; depreciation (only, impairment stripped; null if not split); maintenance_capex (0 if undisclosed); restructuring_charges (0 if none); inventory_change; receivables_change; interest_paid; income_tax_paid; revenue; net_income; total_debt; cash_and_equivalents (cash only); short_term_investments; non_current_marketable_securities (includes non-marketable strategic stakes; 0 if none); diluted_shares_outstanding (millions); total_shareholders_equity (controlling only); total_assets; goodwill; intangible_assets (net, ex-goodwill); risk_flags; r_and_d_expenses (0 for financials; null if undisclosed); share_repurchases ($, positive); buyback_shares (millions); dividends_paid ($, positive); end_of_period_price (null — user fills).
- ttm_build_notes — brief note on estimates, data gaps, or assumptions. MUST note if Interest/Income Tax used the 10-K annual instead of a TTM build, and if Buyback Shares used a 10-K annual proxy.

EMIT-TIME SELF-CHECK (do this before printing the JSON): confirm the JSON's first two fields are a non-null "fiscal_year_label" and a real YYYY-MM-DD "fiscal_year_end" (the most-recent quarter-end) that matches the date you stated in the scratchpad. The fiscal_year_end must NOT be the literal "YYYY-MM-DD" and must NOT be null. If you cannot produce a real quarter-end date, STOP and say so rather than emitting a placeholder.

TTM output template (32 fields, snake_case — note the extra ttm_build_notes):
```json
{
  "fiscal_year_label": "TTM-2026",
  "fiscal_year_end": "2026-03-31",
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

The example fiscal_year_end above ("2026-03-31") is illustrative — replace it with the actual most-recent quarter-end from your 10-Q. Do not emit it unchanged unless that is genuinely the quarter-end.

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

- This prompt and the Annual prompt are intentionally separate, single-purpose, and mode-locked. The file you paste IS the mode — there is no auto-detection to misread.
- Field meanings are kept inline here (NotebookLM only sees what you paste), but the canonical schema lives in the REPA Field Dictionary v1.0. When a definition changes, update the dictionary, this prompt, and the Annual prompt together.
- The single most important hardening vs. v1.1: a real example quarter-end date instead of the "YYYY-MM-DD" placeholder, plus the mandatory/never-null identity rule and the emit-time self-check — this is the field whose omission silently corrupts the active pill.

*TTM v2.0 — split from NotebookLM Extraction Prompt v1.1.*
