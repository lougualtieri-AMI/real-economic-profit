# ⚡ Real Economic Profit Analyzer

**Cut through accounting noise — find the real cash yield of a business.**

A single-page web app that calculates what companies *actually* earn in cash, stripping away accounting distortions in SEC filings. Built for investors who want to see past reported earnings to the real economics.

🔗 **[Launch the App](https://lougualtieri-ami.github.io/real-economic-profit/)**

---

## What It Does

Wall Street's standard metrics — EPS, Free Cash Flow, P/E — are distorted by stock-based compensation, acquisition accounting, buyback-compressed equity, and "one-time" charges that recur every year. This tool fixes that.

```
Real Economic Profit = Cash From Operations
                     − Real SBC (MAX of expense vs grant value)
                     − Maintenance CapEx
                     − Restructuring
                     + Investment Income
```

The app provides both a **Full Cost** view (full D&A as maintenance) and an **Excluding Amortization** view (physical depreciation only, excluding acquisition amortization) — critical for evaluating companies that grow through M&A.

Every Export Brief includes a self-contained appendix explaining the methodology, so anyone receiving the analysis can understand it without prior context.

---

## Features

* **Multi-Year Trends** — Import 2+ years to see ROIC trajectory, profit trends, and growth decomposition
* **"When Does This Investment Work?"** — Model revenue growth scenarios to see when (or if) your yield hits 10% or 15%
* **Full Cost & Excluding Amortization** — Dual views for acquisition-heavy companies, shown side by side
* **Three-Tier Yield Display** — Wall Street Earnings Yield → Real Earnings Yield → Real Ownership Yield
* **Portfolio Scorecard** — Rank and compare companies across all metrics
* **Red Flags** — Automated alerts for SBC dilution, leverage, ROIC compression, and off-balance-sheet risks
* **Export Brief** — One-click HTML/Markdown report with full appendix — ready for NotebookLM, Google Docs, or sharing

---

## Data Pipeline

```
SEC Filing (10-K / 10-Q)
        ↓
  NotebookLM / Gemini  →  extracts structured JSON (large context window handles full filings)
        ↓
  Paste JSON into the app  →  Real Economic Profit Analysis
        ↓
  Claude (audit prompt)  →  cross-checks extraction, explains findings, generates Gemini research prompt
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
6. Claude audits the data, generates a Gemini research prompt, and produces three PDF reports after you feed back the Gemini research

### Quarterly Workflow (10-Q TTM)

1. Upload the 10-Q **and** the most recent 10-K to **NotebookLM**
2. Paste the TTM extraction prompt from `10Q_extraction.md`
3. Copy the JSON → paste into the app alongside the prior period JSON
4. Open a new Claude chat, paste `CLAUDE_QUARTERLY_AUDIT_v1_3.md` + both JSONs
5. Claude audits the TTM data, generates a Gemini research prompt, and produces a Quarterly Brief PDF after you feed back the Gemini research

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

Enter ticker `TEST`, price `100`, click Analyze.

---

## Key Insights This Tool Reveals

* **SBC is the hidden tax** — many tech companies have SBC running 15–25% of CFO. Grant value often exceeds the expense by 2–3x. The app uses the higher of the two to capture real dilution.
* **Reported FCF overstates reality** — traditional FCF doesn't deduct SBC dilution. The Real vs. Reported Gap shows how much.
* **Acquisitive companies need two views** — Broadcom shows negative Real Profit (Full Cost) but positive (Excluding Amortization), because 93% of their D&A is acquisition amortization. The app shows both and explains which is more appropriate.
* **Real P/E is usually higher than traditional P/E** — typically 1.3–2x for quality companies. The brief's appendix explains why.
* **Net cash materially changes the ownership yield** — for cash-rich companies (e.g., Alphabet, Microsoft), Enterprise Value is meaningfully lower than Market Cap, making the ownership yield higher than the earnings yield.

---

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The complete app — single file, no dependencies (v3.4.8) |
| `10_K_extraction.md` | Gemini/NotebookLM prompt for annual 10-K extraction (v3.2) |
| `10Q_extraction.md` | Gemini/NotebookLM prompt for quarterly 10-Q TTM extraction (v1.2) |
| `CLAUDE_AUDIT_PROMPT_v4_2.md` | Claude audit prompt for annual 10-K filings |
| `CLAUDE_QUARTERLY_AUDIT_v1_3.md` | Claude audit prompt for quarterly TTM updates |
| `REP_Glossary.md` | Full glossary of all metrics and terms |
| `README.md` | This file |

---

## Sharing

* **Share the app**: Anyone can use the [GitHub Pages link](https://lougualtieri-ami.github.io/real-economic-profit/)
* **Share analyses**: Export Briefs are self-contained with methodology appendix
* **Share portfolios**: ⬇ Backup / ⬆ Restore portfolio as JSON
* **Your data is private**: Everything lives in your browser's localStorage

---

## Version History

| Version | Date | Key Changes |
| --- | --- | --- |
| v3.4.8 | Mar 5, 2026 | Fixed net debt narrative (now correctly subtracts Cash + STI, not just Cash) |
| v3.4.7 | Mar 5, 2026 | Terminology: "Conservative" → "Full Cost", "Asset-Light/Heavy" → "Low/High Intensity" throughout; fixed year selection default bug |
| v3.4.6 | Prior | SBC deep-dive, CapEx maintenance/growth decomposition, amortization classification, R&D decomposition |

---

*Built by Lou Gualtieri. Vanilla HTML/JS — no frameworks, no backend, no tracking. Analysis framework inspired by owner-earnings concepts from Buffett and Mauboussin, adapted for modern tech and acquisition-heavy companies.*
