# ⚡ Real Economic Profit Analyzer (REPA)

**Cut through accounting noise — find the real ownership yield of a business.**

A single-page web app that calculates what a company *actually* earns in cash, stripping away the accounting distortions buried in SEC filings, and then translates that into the return you'd expect to earn as an owner. Built for investors who want to see past reported earnings to the real economics — and past today's yield to the forward total return.

🔗 **[Launch the App](https://lougualtieri-ami.github.io/real-economic-profit/)**

---

## What It Does

Wall Street's standard metrics — EPS, Free Cash Flow, P/E — are distorted by stock-based compensation, acquisition amortization, buyback-compressed equity, and "one-time" charges that recur every year. REPA fixes that, starting from cash and adjusting for the things GAAP gets wrong:

```
Real Economic Profit (REP) = Cash From Operations
                           − Real SBC          (the MAX of grant-date fair value vs. expense)
                           − Maintenance CapEx (D&A × 1.05 by default; multiplier adjustable)
                           − Restructuring     ("one-time" charges that recur)
                           + Investment Income
```

The maintenance-CapEx charge uses a **physical-split cap**: when a company's Depreciation-Only figure is available, that physical depreciation (not total D&A) sets the floor on maintenance — so acquisition-heavy names aren't over-charged for amortization that isn't a real upkeep cost.

From REP, the app builds a **three-tier yield cascade** so you can see the gap between what's reported and what you'd really earn:

1. **Wall Street Earnings Yield** — net income ÷ market cap (what most investors see)
2. **Real Earnings Yield** — REP per share ÷ price (the same price, earnings cleaned up)
3. **Real Ownership Yield** — REP ÷ enterprise value (what you'd earn buying the whole company, debt and cash included)

Every Export Brief carries a self-contained methodology appendix, so anyone you share it with can follow the analysis without prior context.

---

## The Best Guess View

Amortization and growth R&D are judgment calls, not facts — so REPA shows three reference points side by side and lets you tune the one that reflects your thesis:

* **① Full D&A** — most conservative; treats all depreciation *and* amortization as a real maintenance cost.
* **② 100% Amortization Added Back** — physical depreciation only; excludes acquisition amortization entirely. Critical for companies that grow through M&A.
* **③ Best Guess ★** — the slider-calibrated central case sitting between the two.

Two sliders in **Best Guess Controls** drive Panel ③:

* **Amortization add-back %** — how much acquisition amortization to treat as a non-cash artifact rather than a real cost.
* **Growth-R&D add-back %** — how much of R&D is *growth* investment (funding new products/markets) rather than *maintenance*. The growth portion is added back after tax and, in Best Guess views, capitalized into the ROIC base on a sector asset life (Software 3y / Semiconductor 5y / Pharma 7y) for a symmetric return.

A **D&A Multiplier** in the inputs (default 1.05×) sets the inflation buffer on maintenance CapEx and feeds the base REP for *every* view.

---

## Future Total Return (the forward headline)

Today's yield is only half the picture. The **Future Total Return** panel models the return you'd actually compound as an owner using one identity:

```
k = (1 − b) · y  +  b · r
```

* **y** — today's real earnings yield (what you collect now)
* **b** — the share of profit the company reinvests
* **r** — the return that reinvested profit earns
* **b · r = g** — the organic growth that reinvestment produces
* **k** — your expected annual total return: the cash leg you collect plus the growth the business compounds — *if the multiple holds.*

The whole figure rests on those assumptions holding, and the panel keeps that front and center. The same **b** flows into the Buyback ROI panel, where every repurchase vintage shows its **REP Growth (g)** and **Total Return (k)** so the two panels never disagree.

---

## Features

* **Three-Tier Yield Cascade** — Wall Street → Real Earnings → Real Ownership
* **Best Guess View** — Full D&A / 100% Amort / Best Guess ★, slider-calibrated
* **Future Total Return** — the `k = (1−b)·y + b·r` forward total-return identity
* **Capital Allocation Analysis** — Real ROIC and ROIIC trends, REP·IC·NTA co-movement (indexed, log-axis), and **Buyback ROI** with per-vintage yield-on-cost, REP Growth, and Total Return
* **Historical** — curated key-metrics summary plus long-run yield/margin/revenue trends and full data tables (with per-table year windows for long histories)
* **Look-Through REP** — for companies whose equity portfolios generate unreported earnings power (e.g. Berkshire's retained-earnings look-through; an Alphabet fair-value proxy)
* **"When Does This Investment Work?"** — model revenue-growth scenarios to see when (or if) your yield hits a target
* **Target Price Calculator** — flips the equation: the price you'd need for the yield you want
* **Red Flags** — automated alerts for SBC dilution, leverage, ROIC compression, and off-balance-sheet risk
* **Maintenance-CapEx advisories** — a deferred-maintenance note (live trend table *and* the Export Brief) when a company spent less on upkeep than the estimate, plus a red "Depreciation Only missing" data-completeness nudge for acquisition-heavy names with that field left blank
* **Export Brief** — one-click HTML/Markdown report with a full methodology appendix — ready for NotebookLM, Google Docs, or sharing
* **Portfolio Scorecard** — rank and compare companies across all metrics

---

## How to Read the App

The app is laid out **grounded-first, forward-second** — everything built on reported numbers comes before anything that depends on assumptions:

1. **Inputs** (collapsible) — Phase 1 Cash Flow and Phase 2 Balance Sheet
2. **Yield Ladder** — the three-tier cascade and the Best Guess panels
3. **Profit Waterfall + Best Guess Controls** — how REP is built, with the deep-dives (SBC, CapEx, amortization, R&D) and the sliders
4. **Capital Allocation** → **Buybacks** → **Historical** — the grounded track record
5. *— forward-looking divider —*
6. **Future Total Return** — the forward synthesis
7. **Red Flags** → **Detailed Breakdown** (all-years appendix)
8. **When Does This Investment Work?** and **Target Price Calculator** — optional what-if tools

---

## Data Pipeline

```
SEC Filing (10-K / 10-Q)
        ↓
  (optional) Debulk prompt  →  distill the filing to the ~30 pages REPA needs
        ↓
  NotebookLM / Gemini  →  extraction master → structured JSON (10-K or TTM mode)
        ↓
  Paste JSON into the app  →  Real Economic Profit analysis
        ↓
  Claude (audit master)  →  cross-checks extraction, explains findings, generates a Gemini research prompt
        ↓
  Gemini Deep Research  →  post-filing developments, risk updates, forward guidance
        ↓
  Claude  →  synthesizes research + financials into PDF reports (optional annotated companion)
```

Both cycles below use the **same two master prompts** — `NOTEBOOK_EXTRACTION_PROMPT` and `CLAUDE_AUDIT_PROMPT`. Each infers its mode from the inputs you paste, so there's no separate file per cycle.

### Annual Cycle (10-K)

1. *(Optional)* Run `DEBULKED_10K_PROMPT` in **NotebookLM** to distill the filing first.
2. Paste `NOTEBOOK_EXTRACTION_PROMPT` (10-K mode) → copy the JSON → paste into the app, enter ticker and share price → **Analyze**. Repeat for 2–3 years for trend analysis.
3. Open a new Claude chat, paste `CLAUDE_AUDIT_PROMPT` + the JSON. It runs in **ANNUAL** mode (or **INITIATION** mode for a brand-new portfolio entrant), audits field-by-field, generates a Gemini research prompt, and produces the PDF reports after you feed the research back.

### Quarterly Cycle (10-Q TTM)

1. Upload the 10-Q **and** the most recent 10-K to **NotebookLM**; run `NOTEBOOK_EXTRACTION_PROMPT` (TTM mode).
2. Copy the JSON → paste into the app alongside the prior-period JSON.
3. Open a new Claude chat, paste `CLAUDE_AUDIT_PROMPT` + both JSONs. It runs in **QUARTERLY** mode and produces a Quarterly Brief after you feed the research back.

Two specialist prompts sit outside the routine cycle: `CLAUDE_PORTFOLIO_JSON_AUDIT` (a lightweight JSON-in/JSON-out integrity check, no PDFs), `CLAUDE_HISTORICAL_BACKTEST_PROMPT` (a hindsight-blinded backtest at a past period), and `Post_Report_3_Annotation_Prompt` (an annotated companion to a Gemini report). Field definitions for every term live in `REP_Glossary.md`.

### Try It Now (Sample Data)

Paste this into the Import field to test without a real filing:

```json
{
  "fiscal_year_label": "FY2024",
  "fiscal_year_end": "2024-12-31",
  "cash_from_operations": 50000,
  "sbc_expense_addback": 5000,
  "sbc_grant_date_fair_value": 6000,
  "total_capex": 20000,
  "d_and_a": 8000,
  "depreciation": 6000,
  "revenue": 150000,
  "net_income": 30000,
  "total_debt": 25000,
  "cash_and_equivalents": 15000,
  "short_term_investments": 5000,
  "diluted_shares_outstanding": 1000,
  "total_shareholders_equity": 80000,
  "total_assets": 200000,
  "goodwill": 10000,
  "intangible_assets": 3000,
  "risk_flags": "No material risks identified."
}
```

Enter ticker `TEST`, price `100`, click **Analyze**.

---

## Key Insights This Tool Reveals

* **SBC is the hidden tax** — many tech companies run SBC at 15–25% of CFO, and grant value often exceeds the expense by 2–3×. REPA uses the higher of the two to capture real dilution.
* **Reported FCF overstates reality** — traditional FCF doesn't deduct SBC dilution; the Real vs. Reported Gap shows how much.
* **Acquisitive companies need two views** — a name can show negative Real Profit on Full D&A but positive once acquisition amortization is added back. REPA shows both and explains which fits.
* **Real P/E is usually higher than reported P/E** — typically 1.3–2× for quality companies; the brief's appendix explains why.
* **Net cash lifts the ownership yield** — for cash-rich companies, enterprise value is well below market cap, so the ownership yield runs above the earnings yield.
* **Yield is not return** — a 5% yield on a business reinvesting at high returns can compound to a double-digit total return; the Future Total Return panel separates the two.

---

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The complete app — single file, no dependencies (v4.16.0) |
| `README.md` | This file |
| `REP_Glossary.md` | Full glossary of every metric, yield, ratio, and input field |
| `NOTEBOOK_EXTRACTION_PROMPT_v1_1.md` | Extraction master — turns a filing into structured JSON (10-K and TTM modes) |
| `CLAUDE_AUDIT_PROMPT_v1_0.md` | Audit master — ANNUAL / QUARTERLY / INITIATION modes; infers mode from the inputs you paste |
| `DEBULKED_10K_PROMPT_v1_3.md` | Optional pre-process — distill a 150+ page filing to the ~30 pages REPA needs before extraction |
| `CLAUDE_PORTFOLIO_JSON_AUDIT_v1_1.md` | Lightweight JSON-in / JSON-out integrity check and master-portfolio integration (no PDFs) |
| `CLAUDE_HISTORICAL_BACKTEST_PROMPT_v2_1.md` | Hindsight-blinded backtest of REPA metrics at a past period (research use) |
| `Post_Report_3_Annotation_Prompt_v1_3.md` | Annotated companion to a Gemini Deep Research report |

---

## Sharing

* **Share the app** — anyone can use the [GitHub Pages link](https://lougualtieri-ami.github.io/real-economic-profit/)
* **Share analyses** — Export Briefs are self-contained with a methodology appendix
* **Share portfolios** — ⬇ Backup / ⬆ Restore your portfolio as JSON
* **Your data is private** — everything lives in your browser's localStorage

---

## Version History

| Version | Date | Key Changes |
| --- | --- | --- |
| v4.16.0 | Jun 2026 | Maintenance-CapEx **physical-split cap** (uses Depreciation Only as the physical base when available, protecting acquisition-heavy names from over-charged maintenance); **deferred-maintenance advisory** in both the live trend table and the Export Brief; red **"Depreciation Only missing"** data-completeness nudge for acquirer-shaped names with that field blank (supersedes the amber note for those years); renamed the third yield tier to **Real Ownership Yield** app-wide; added a **Total Return** column to the report's Buyback Vintage Yield Matrix for parity with the live card; removed dead glossary text |
| v4.15.0 | Jun 2026 | Future Total Return panel (`k = (1−b)·y + b·r`); Best Guess slider-calibrated view (amort + growth-R&D add-back) with symmetric R&D-capitalized ROIC; Capital Allocation Analysis (ROIC/ROIIC, REP·IC·NTA indexed log chart, Buyback ROI with REP Growth & Total Return); Look-Through REP; grounded→forward layout |
| v3.4.8 | Mar 2026 | Fixed net-debt narrative (subtracts Cash + STI, not just Cash) |
| v3.4.7 | Mar 2026 | Terminology pass; fixed year-selection default bug |
| v3.4.6 | Prior | SBC deep-dive, CapEx maintenance/growth decomposition, amortization classification, R&D decomposition |

---

*Built by Lou Gualtieri. Vanilla HTML/JS — no frameworks, no backend, no tracking. The framework draws on owner-earnings concepts from Buffett and Mauboussin, adapted for modern tech and acquisition-heavy companies, and extends them into a forward total-return identity: `k = (1−b)·y + b·r`.*
