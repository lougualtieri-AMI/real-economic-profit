# ⚡ Real Economic Profit Analyzer (REPA)

**Cut through accounting noise — find the real cash yield of a business.**

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
  NotebookLM / Gemini  →  extracts structured JSON (large context handles full filings)
        ↓
  Paste JSON into the app  →  Real Economic Profit analysis
        ↓
  Claude (audit prompt)  →  cross-checks extraction, explains findings, generates a Gemini research prompt
        ↓
  Gemini Deep Research  →  post-filing developments, risk updates, forward guidance
        ↓
  Claude  →  synthesizes research + financials into PDF reports
```

### Annual Workflow (10-K)

1. Upload the 10-K to **NotebookLM** (or Gemini)
2. Paste the extraction prompt from `10_K_extraction.md`
3. Copy the JSON → paste into the app → enter ticker and share price → **Analyze**
4. Repeat for additional years (2–3 years recommended for trend analysis)
5. Open a new Claude chat, paste `CLAUDE_AUDIT_PROMPT_v4_2.md` + the JSON output
6. Claude audits the data, generates a Gemini research prompt, and produces the PDF reports after you feed back the Gemini research

### Quarterly Workflow (10-Q TTM)

1. Upload the 10-Q **and** the most recent 10-K to **NotebookLM**
2. Paste the TTM extraction prompt from `10Q_extraction.md`
3. Copy the JSON → paste into the app alongside the prior-period JSON
4. Open a new Claude chat, paste `CLAUDE_QUARTERLY_AUDIT_v1_3.md` + both JSONs
5. Claude audits the TTM data, generates a Gemini research prompt, and produces a Quarterly Brief PDF after you feed back the research

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
| `index.html` | The complete app — single file, no dependencies (v4.15.0) |
| `10_K_extraction.md` | Gemini/NotebookLM prompt for annual 10-K extraction |
| `10Q_extraction.md` | Gemini/NotebookLM prompt for quarterly 10-Q TTM extraction |
| `CLAUDE_AUDIT_PROMPT_v4_2.md` | Claude audit prompt for annual 10-K filings |
| `CLAUDE_QUARTERLY_AUDIT_v1_3.md` | Claude audit prompt for quarterly TTM updates |
| `REP_Glossary.md` | Full glossary of all metrics and terms |
| `README.md` | This file |

> **Note:** confirm the prompt filenames/versions above against the repo — the audit and extraction prompts have advanced since this table was last set, and any renamed or newer versions should be reflected here.

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
| v4.15.0 | Jun 2026 | Future Total Return panel (`k = (1−b)·y + b·r`); Best Guess slider-calibrated view (amort + growth-R&D add-back) with symmetric R&D-capitalized ROIC; Capital Allocation Analysis (ROIC/ROIIC, REP·IC·NTA indexed log chart, Buyback ROI with REP Growth & Total Return); Look-Through REP; grounded→forward layout |
| v3.4.8 | Mar 2026 | Fixed net-debt narrative (subtracts Cash + STI, not just Cash) |
| v3.4.7 | Mar 2026 | Terminology pass; fixed year-selection default bug |
| v3.4.6 | Prior | SBC deep-dive, CapEx maintenance/growth decomposition, amortization classification, R&D decomposition |

---

*Built by Lou Gualtieri. Vanilla HTML/JS — no frameworks, no backend, no tracking. The framework draws on owner-earnings concepts from Buffett and Mauboussin, adapted for modern tech and acquisition-heavy companies, and extends them into a forward total-return identity: `k = (1−b)·y + b·r`.*
