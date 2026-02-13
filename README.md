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

The app provides both a **Conservative** view (full D&A as maintenance) and an **Asset-Light** view (physical depreciation only, excluding acquisition amortization) — critical for evaluating companies that grow through M&A.

Every Export Brief includes a self-contained appendix explaining the methodology, so anyone receiving the analysis can understand it without prior context.

---

## Features

- **Multi-Year Trends** — Import 2+ years to see ROIC trajectory, profit trends, and growth decomposition
- **"When Does This Investment Work?"** — Model revenue growth scenarios to see when (or if) your yield hits 10% or 15%
- **Conservative & Asset-Light** — Dual views for acquisition-heavy companies, shown side by side
- **Portfolio Scorecard** — Rank and compare companies across all metrics
- **Red Flags** — Automated alerts for SBC dilution, leverage, ROIC compression, and off-balance-sheet risks
- **Export Brief** — One-click HTML/Markdown report with full appendix — ready for NotebookLM, Google Docs, or sharing

---

## Data Pipeline

```
SEC Filing (10-K / 10-Q)
        ↓
  Google Gemini  →  extracts structured JSON (large context window handles full filings)
        ↓
  Paste JSON into the app  →  Real Economic Profit Analysis
        ↓
  Claude  →  optional audit (cross-checks extraction, explains findings, flags risks)
```

### Quick Start

1. Open a company's **10-K** in [Google Gemini](https://gemini.google.com/)
2. Paste the extraction prompt from `10_K_extraction.md`
3. Copy the JSON → paste into the app → enter ticker and share price → **Analyze**
4. Repeat for additional years and companies
5. Export the brief, audit with Claude if desired

### Try It Now (Sample Data)

Paste this into the Import field to test without a real filing:

```json
{
  "Fiscal Year Label": "FY2024",
  "Fiscal Year End": "2024-12-31",
  "Cash From Operations": 50000,
  "SBC Expense addback": 5000,
  "SBC Grant Date Fair Value": 6000,
  "Total CapEx": 20000,
  "D&A": 8000,
  "Depreciation": 6000,
  "Revenue": 150000,
  "Net Income": 30000,
  "Total Debt": 25000,
  "Cash & Equivalents": 15000,
  "Short-Term Investments": 5000,
  "Diluted Shares Outstanding": 1000,
  "Total Shareholders Equity": 80000,
  "Total Assets": 200000,
  "Goodwill": 10000,
  "Intangible Assets": 3000,
  "Risk Flags": "No material risks identified."
}
```

Enter ticker `TEST`, price `100`, click Analyze.

---

## Key Insights This Tool Reveals

- **SBC is the hidden tax** — many tech companies have SBC running 15–25% of CFO. Grant value often exceeds the expense by 2–3x.
- **Reported FCF overstates reality** — traditional FCF doesn't deduct SBC dilution. The Real vs. Reported Gap shows how much.
- **Acquisitive companies need two views** — Broadcom shows negative Real Profit (conservative) but positive (asset-light), because 93% of their D&A is acquisition amortization.
- **Real P/E is usually higher than traditional P/E** — typically 1.3–2x for quality companies. The brief's appendix explains why.

---

## Files

| File | Purpose |
|------|---------|
| `index.html` | The complete app — single file, no dependencies |
| `10_K_extraction.md` | Gemini prompt for annual 10-K extraction |
| `10Q_extraction.md` | Gemini prompt for quarterly 10-Q extraction |
| `CLAUDE_AUDIT_PROMPT.md` | Claude audit prompt |
| `REP_Glossary.md` | Full glossary of all metrics and terms |
| `README.md` | This file |

---

## Sharing

- **Share the app**: Anyone can use the [GitHub Pages link](https://lougualtieri-ami.github.io/real-economic-profit/)
- **Share analyses**: Export Briefs are self-contained with methodology appendix
- **Share portfolios**: ⬇ Backup / ⬆ Restore portfolio as JSON
- **Your data is private**: Everything lives in your browser's localStorage

---

*Built by Lou Gualtieri. Vanilla HTML/JS — no frameworks, no backend, no tracking. Analysis framework inspired by owner-earnings concepts from Buffett and Mauboussin, adapted for modern tech and acquisition-heavy companies.*
