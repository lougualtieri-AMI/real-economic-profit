# Claude Historical Period Backtest Prompt — Real Economic Profit Analyzer
## Version 2.1 | May 26, 2026
## Purpose: Discontinuous historical sampling for REPA backtesting studies

Changelog from v2.0:
- Cross-references updated to the Claude Audit Prompt (v1.0) and the REPA Field Dictionary (v1.0). The separate Annual/Quarterly/Mid-Year prompts and the standalone "31-field schema" are retired; the dictionary is now the canonical field set.

Changelog from Historical Snapshot v1.0:
- Renamed: "Historical Snapshot" → "Historical Period Backtest" to reflect actual use case
- Removed Gemini Deep Research step entirely (cannot be reliably blinded to post-period events)
- Replaced with optional contemporaneous evidence section (manually supplied by user)
- Aligned to current 31-field JSON schema
- Incorporated framework rules established post-v1.0: multi-year SBC grant division, restructuring two-line sum, TTM null-grant fallback
- File numbering moved to **30-series** (clear separation from active 00–04 reports and 20-series legacy)
- Audit log structure preserved (Part 7) — this is the prompt's reason for existing

---

## ⚠️ CRITICAL FRAMING — READ FIRST

This is a **HISTORICAL BACKTEST**, not a current or forward-looking analysis.

- All calculations use the **price entered for the target period** — not today's price
- All metrics are **trailing 12 months** as of the period end date
- This report contains **no investment opinion, no forecast, and no forward guidance**
- The price used reflects the **first week following the end of the fiscal period**
- **No Gemini Deep Research step** — the qualitative leg cannot be reliably blinded to post-period events. If contemporaneous qualitative context is desired, the user supplies it manually (analyst reports, news from the period, proxy materials).
- This report is **factual and mechanical** — it documents what REPA would have shown at this moment in history, knowing only what was knowable at the time

---

## WHEN TO USE THIS PROMPT

Use this prompt when you want to ask:

> *"What would REPA have shown for [Company] at [Historical Period], using only information available at that time?"*

Typical use cases:
- Backtesting REPA's predictive validity (e.g., GE 2002, Citigroup 2006, Lehman 2007)
- Sampling pre-crisis financial profiles for stress-testing the framework
- Building a discontinuous historical series for a single company across non-adjacent years
- Comparing REPA signals at different points in a market cycle

Do NOT use this prompt for:
- Continuous multi-year history of a current portfolio company → use the Claude Audit Prompt (v1.0) in ANNUAL mode, repeatedly
- Mid-year initiation of a new portfolio company → use the Claude Audit Prompt (v1.0) in INITIATION mode
- Routine quarterly updates → use the Claude Audit Prompt (v1.0) in QUARTERLY mode

---

## PART 0 — AUDIT LOG CHECK (RUN FIRST, EVERY TIME)

Before any calculations, check whether an audit log exists for this company.

### 0A — If an audit log is provided (`31_[TICKER]_Backtest_Audit_Log.md`):

Read the log and identify:
- Which periods are already marked **CERTIFIED ✓**
- Which periods are **new or uncertified**

**For certified periods:** Accept the previously verified figures as accurate. Do not re-audit. Pull recorded metrics directly into the multi-period context table (Part 4). State: *"[Period] — figures from certified audit log dated [date]. No re-verification performed."*

**For uncertified periods only:** Perform full JSON verification and REP calculation (Parts 2 and 3).

At the end of the report, provide an **Updated Audit Log Block** (Part 7) reflecting any newly certified periods.

### 0B — If no audit log exists:

This is a first-run for this company. Perform full verification on all loaded periods. Generate a complete audit log block at the end (Part 7) certifying all periods audited in this session.

### 0C — Efficiency rule

Never ask the user to re-verify a certified period. The log is the authority. If a certified period's JSON has been deliberately changed by the user, they will indicate this and the certification should be updated accordingly.

---

## PART 1 — PERIOD IDENTIFICATION

State clearly at the top of the report:

```
HISTORICAL REPA BACKTEST
Company: [Full Name] ([TICKER])
Period: [Fiscal Year / Quarter] ending [exact date]
Price Used: $[X.XX] — first week following period end ([approximate date])
Price Basis: PERIOD-END HISTORICAL (not current market price)
Report Generated: [today's date]
Hindsight Status: BLINDED — analysis uses only data knowable at period end
```

This block must appear on every page header in abbreviated form:
`HISTORICAL BACKTEST | [TICKER] | [Period] | Price: $X.XX | Blinded`

---

## PART 2 — DATA INPUTS AND SBC ADJUSTMENT

### 2A — Confirm Data Loaded
List the years/periods present in the JSON data. Identify:
- **Target period** (primary calculation period)
- **Prior periods** (context years loaded alongside, if any)

Confirm the JSON conforms to the REPA Field Dictionary (v1.0). If an older/partial schema is provided (e.g., pre-buyback fields), note which fields are unavailable and proceed with what is provided.

### 2B — SBC Adjustment Rules (apply current framework)

Apply the standard MAX convention: `Real SBC = MAX(SBC expense, grant-date fair value)`.

Additional rules (codified post-v1.0, apply retroactively to historical periods):

1. **Multi-year grants:** If the period's grant value reflects a multi-year award, divide by the number of years before applying MAX. State this explicitly if applicable.

2. **TTM null grant fallback:** If the target period is a TTM and grant value is null, use the most recent prior 10-K adjusted grant value as a floor. Flag explicitly.

3. **Pre-2006 data:** SBC expense was not consistently disclosed before SFAS 123R (effective fiscal years beginning after June 15, 2005). For periods before this, SBC may be incomplete or based on pro forma footnote disclosure. Flag this prominently — REPA's SBC adjustment is structurally less reliable for pre-2006 periods.

### 2C — Restructuring Charges

Apply the two-line rule: restructuring must sum BOTH income statement lines (operating restructuring + any separately disclosed restructuring within COGS or other operating expense). Historical filings (especially 2000s era) often split these — verify carefully.

---

## PART 3 — REPA CALCULATIONS (TARGET PERIOD)

### 3A — Enterprise Value
```
Market Cap         = Shares Outstanding × Period-End Price
(+) Total Debt
(−) Cash & Equivalents
(−) Short-Term Investments
= Enterprise Value
```
State each component explicitly. Flag any unusual items (e.g., large pension obligations, off-balance-sheet financing common in pre-2008 filings).

### 3B — Real Economic Profit (REP) Build
Show each line:
```
Cash from Operations (CFO)
(−) Real SBC                    [state value used and source — flag if pre-2006]
(−) Maintenance CapEx           [D&A × 1.05 for Full Cost view]
(−) Restructuring Charges       [confirm two-line sum applied]
= REP (Full Cost)

(−) Maintenance CapEx Ex-Amort  [Depreciation only × 1.05, if applicable]
= REP (Ex-Amortization)         [show only if amortization is material]
```

Material amortization threshold: amortization > 10% of total D&A.

### 3C — Three-Tier Yield Cascade
Calculate and display all three yields for the target period:

| Metric | Value | Basis |
|--------|-------|-------|
| GAAP Earnings Yield | X.X% | GAAP Net Income / Market Cap |
| Real Earnings Yield | X.X% | REP / Market Cap |
| Real Ownership Yield | X.X% | REP / Enterprise Value |

### 3D — GAAP vs. REPA Divergence
State the spread between GAAP Earnings Yield and Real Ownership Yield.

Classify the divergence:
- **< 1%** — Minimal divergence, GAAP is a reasonable proxy
- **1–3%** — Moderate divergence, note primary driver
- **> 3%** — Significant divergence, explain in one sentence what drives it (SBC, amortization, CapEx intensity, net cash position, restructuring)

This is factual — do not characterize the divergence as good or bad.

---

## PART 4 — MULTI-PERIOD CONTEXT TABLE

Display a summary table for all loaded periods (target year + prior years if provided):

| Period | Price Used | Market Cap | EV | REP | Real Yield | Real Own. Yield | GAAP EY |
|--------|-----------|------------|----|----|-----------|-----------------|---------|
| [Y-3]  | $X.XX*    | $XB        | $XB| $XB| X.X%     | X.X%            | X.X%    |
| [Y-2]  | $X.XX*    | $XB        | $XB| $XB| X.X%     | X.X%            | X.X%    |
| [Y-1]  | $X.XX*    | $XB        | $XB| $XB| X.X%     | X.X%            | X.X%    |
| **[TARGET]** | **$X.XX** | **$XB** | **$XB** | **$XB** | **X.X%** | **X.X%** | **X.X%** |

*If period-end prices for prior years were not provided, use the target period price for all years and note: "Prior year yields calculated using target period price — for trend comparison of REP only, not yield."*

Target period row should be visually distinguished (bold, shaded, or marked).

---

## PART 5 — FACTUAL OBSERVATIONS

**Maximum 3 bullet points. Factual only. No opinion, no forecast, no hindsight.**

Permitted observations:
- Direction of REP trend over the loaded periods (rising / falling / stable)
- Whether GAAP yield divergence is widening or narrowing over time
- Any notable single-period anomaly visible in the data (e.g., restructuring charge, acquisition, pension contribution)
- SBC source and any year-over-year change in grant value
- Pre-2006 SBC reliability flag if applicable

**Strictly not permitted:**
- Any statement informed by what happened after the period end
- Any statement about whether the stock was cheap or expensive
- Any forward-looking language
- Any comparison to other companies (unless explicitly part of a backtest study supplied by user)
- Any investment recommendation or implication
- Any reference to subsequent events the analyst at the time would not have known

---

## PART 6 — CONTEMPORANEOUS EVIDENCE (OPTIONAL)

If the user has supplied contemporaneous qualitative materials from the period (analyst reports, news clippings, proxy materials, earnings call transcripts dated ≤ period end), summarize them factually here in 1–2 paragraphs.

If no contemporaneous materials are provided, state: *"No contemporaneous qualitative evidence supplied. This backtest is quantitative only."* and skip the section.

**Strict rule:** Do NOT introduce information from sources dated after the period end, regardless of how relevant or interesting. The point of the backtest is contamination-free analysis.

---

## PART 7 — DATA INTEGRITY FLAGS

List any of the following if present:
- SBC substitution made (specify source year and reason)
- Pre-2006 SBC disclosure limitation
- Missing or estimated line items
- Unusual CFO items that may distort REP (especially pre-2008 era: securitizations, off-balance-sheet vehicles, pension accounting changes)
- Fiscal year vs. calendar year mismatch
- Any period where prior-year price was unavailable (yield calculation affected)
- Restructuring two-line sum verification status
- Schema version of JSON (current per Field Dictionary v1.0 vs. legacy)

---

## PART 8 — UPDATED AUDIT LOG BLOCK

At the end of every report, output a ready-to-save audit log block in this exact format. The user will copy this into their `31_[TICKER]_Backtest_Audit_Log.md` file.

```
## [TICKER] HISTORICAL REPA BACKTEST AUDIT LOG
Last updated: [date]

| Period      | Status        | Price Used | SBC Source              | Audited    | Notes                        |
|-------------|---------------|------------|-------------------------|------------|------------------------------|
| FY20XX      | CERTIFIED ✓   | $X.XX      | FY20XX 10-K grant       | YYYY-MM-DD | [any flags or none]          |
| FY20XX      | CERTIFIED ✓   | $X.XX      | TTM expense (MAX)       | YYYY-MM-DD | [any flags or none]          |
| FY20XX      | CERTIFIED ✓   | $X.XX      | Pre-2006 pro forma only | YYYY-MM-DD | SBC reliability limited      |
```

**Rules for the log block:**
- Include ALL periods — both previously certified and newly certified
- Preserve exact prices and SBC sources as recorded
- Add a note for any period with a data integrity flag
- Periods audited in this session are marked with today's date
- Previously certified periods retain their original audit date

---

## FORMATTING AND OUTPUT REQUIREMENTS

**Visual identity — must be immediately distinguishable from current and mid-year reports:**

- Title banner: `HISTORICAL REPA BACKTEST` — prominent, distinct styling
- Color scheme: Slate/steel blue accent (distinct from current audit Green #26ae60 and mid-year reports)
- Suggested header color: **Steel #4a5d6f** with **Slate #607080** secondary
- Every page header includes: `HISTORICAL BACKTEST | [TICKER] | [Period] | Price: $X.XX | Blinded`
- Footer on every page: `Backward-looking factual analysis. Price reflects period-end. Hindsight-blinded. Not a current assessment.`
- No forward-looking language anywhere in the document

**File naming (30-series):**
`30_[TICKER]_[FY or Q designation]_[Year]_Backtest.pdf`

Examples:
- `30_GE_FY2002_Backtest.pdf`
- `30_C_FY2006_Backtest.pdf`
- `30_LEH_Q3_2007_Backtest.pdf`

**Length:** Concise. Target 2–3 pages. This is a factual backtest, not a narrative audit.

**Numbers:** All figures in millions ($M) or billions ($B) consistently within the report. Yields to one decimal percent (X.X%).

---

## WHAT THIS REPORT IS NOT

This report is explicitly not:
- A current valuation assessment
- An investment recommendation
- A forecast of future performance
- A comparison to today's price or today's metrics
- A substitute for the current REPA audit (series 00–04)
- A Gemini-augmented qualitative analysis

Its sole purpose is to document what REPA metrics would have shown at a specific historical moment, using the price that prevailed at that time, with no contamination from subsequent events.

---

## RELATIONSHIP TO OTHER REPA PROMPTS

| Prompt | Use Case | Status |
|--------|----------|--------|
| Claude Audit Prompt v1.0 — ANNUAL mode | Most recent 10-K, current portfolio company | Active |
| Claude Audit Prompt v1.0 — QUARTERLY mode | TTM update from 10-Q, existing baseline | Active |
| Claude Audit Prompt v1.0 — INITIATION mode | New company entering portfolio mid-fiscal-year | Active |
| **Historical Backtest (v2.1)** | **Discontinuous historical sampling for backtesting** | **Active — research use** |
| Historical Snapshot (v1.0) | Continuous backward-looking series with Gemini | **Retired** — superseded by v2.0 |

---
*CLAUDE_HISTORICAL_BACKTEST_PROMPT_v2_1 — May 26, 2026*
*Supersedes CLAUDE_HISTORICAL_SNAPSHOT_PROMPT_v1_0*
