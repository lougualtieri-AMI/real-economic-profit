# ⚡ Real Economic Profit Analyzer

**Cut through accounting noise — find the real cash yield of a business.**

A single-page web app that calculates what companies *actually* earn by stripping away accounting distortions in SEC filings. Built for investors who want to see past reported earnings to the real economics.

🔗 **[Launch the App](https://lougualtieri-ami.github.io/real-economic-profit/)**

---

## What It Does

Wall Street's standard metrics — EPS, Free Cash Flow, ROIC — are distorted by stock-based compensation, aggressive capitalization, buyback-compressed equity, and acquisition goodwill. This tool fixes that.

**Real Economic Profit** = Cash From Operations − Real SBC − Maintenance CapEx − Restructuring + Investment Income

Where:
- **Real SBC** = MAX(reported expense, grant date fair value) — captures the true dilution cost
- **Maintenance CapEx** = only what's needed to sustain the asset base (auto-estimated from D&A if not disclosed)
- **Growth CapEx** = the rest — what's actually being invested in growth

### Core Metrics

| Metric | What It Tells You |
|---|---|
| **Real Cash Yield** | What you actually earn on your investment (Real Profit ÷ Enterprise Value) |
| **Real ROIC** | Return on invested capital using real profit, not accounting profit |
| **Return on NTA** | Buyback-neutral return on net tangible assets — strips out equity compression and acquisition goodwill |
| **Incremental Return** | What each dollar of growth CapEx actually produced in new earnings |
| **Reinvestment Rate** | How much capital is being redeployed for growth |
| **Implied Growth** | Reinvestment Rate × ROIC — the organic growth engine |

---

## Features

### 📊 Multi-Year Trend Analysis
Import 2+ years of 10-K data to see how ROIC, profit, and capital efficiency evolve over time. Includes growth decomposition and profit composition showing organic base vs. cumulative reinvestment returns.

### 🏆 Portfolio Scorecard
Ranked comparison across your portfolio with color-coded badges. Pick which companies to include. Instantly see who has the best yield, ROIC, growth profile, and capital efficiency.

### 📊 Full Portfolio Comparison
Side-by-side deep comparison of every metric — from real profit and SBC burden to profit composition and balance sheet structure. Select any subset of companies to compare.

### 🔮 Analyst Projections
Enter buy-side or consensus estimates (revenue, CFO, EPS, target price) and project forward Real Profit, Yield, and ROIC using the same framework. Compares projections to historical patterns and flags divergences.

### 🚩 Red Flags & Risk Scan
Automated alerts for SBC dilution, ROIC compression, buyback distortion, and yield concerns. Also displays off-balance-sheet risk flags (SPVs, VIEs, contingent liabilities, material commitments) extracted directly from SEC filing footnotes.

### 📤 Export Brief
One-click export of the full analysis as a formatted HTML document — ready for Google Docs, NotebookLM, or sharing with colleagues.

---

## How It Works

### Data Flow

```
SEC Filing (10-K / 10-Q)
        ↓
  Google Gemini (extracts structured JSON — large context window handles full filings)
        ↓
  Paste JSON into the app
        ↓
  Real Economic Profit Analysis
        ↓
  Claude (optional audit — cross-checks extraction and explains what you're looking at)
```

### Setup

1. **Open a 10-K** in [Google Gemini](https://gemini.google.com/) (use a separate chat per filing)
2. **Paste the extraction prompt** (see [Extraction Guide](gemini-extraction-guide_10K_6_.md))
3. **Copy the JSON** Gemini returns
4. **Open the app** → click "Import 10-K Data from Gemini" → paste
5. Enter the **ticker** and current **share price**
6. Click **Analyze** → **Save** to portfolio
7. Repeat for additional years and companies

### Why Gemini?

Gemini's large context window can process an entire 10-K filing in one pass, extracting all the data points the app needs. The extraction prompt is designed to pull exactly the right numbers from the right places — cash flow statement, balance sheet, SBC notes, supplemental disclosures, and risk footnotes.

---

## Verification Workflow

The goal is to make SEC filings accessible to regular people — not just analysts. The combination of Gemini (extraction), the app (analysis/visualization), and Claude (interpretation/audit) creates a pipeline where each tool does what it's best at.

### Recommended Audit Process

1. **Gemini extracts** — paste the prompt, get JSON back
2. **Ask Gemini to show its work** — "Show me where you found each number and how you calculated it." Save this to a Google Doc for each filing.
3. **Run the analysis** in the app and hit **Export Brief**
4. **Audit with Claude** — paste the Export Brief AND Gemini's work into Claude using the [Audit Prompt](claude-audit-prompt.md). Claude cross-checks the numbers, explains what you're looking at in plain English, reviews risk flags, and suggests follow-up questions.

This gives you a paper trail: the raw filing → Gemini's extraction with sourcing → the app's analysis → Claude's review.

---

## Quick Start with Sample Data

Want to try it without extracting from filings? Here's a simplified example you can paste into the Import field:

```json
{
  "Fiscal Year Label": "FY2024",
  "Fiscal Year End": "2024-12-31",
  "Cash From Operations": 50000,
  "SBC Expense addback": 5000,
  "SBC Grant Date Fair Value": 6000,
  "Total CapEx": 20000,
  "D&A": 8000,
  "Maintenance CapEx": 0,
  "Restructuring Charges": 0,
  "Inventory Change": 0,
  "Receivables Change": -500,
  "Interest Paid": 1200,
  "Income Tax Paid": 8000,
  "Revenue": 150000,
  "Net Income": 30000,
  "Total Debt": 25000,
  "Cash & Equivalents": 15000,
  "Short-Term Investments": 5000,
  "Non-Current Marketable Securities": 0,
  "Diluted Shares Outstanding": 1000,
  "Total Shareholders Equity": 80000,
  "Total Assets": 200000,
  "Goodwill": 10000,
  "Intangible Assets": 3000,
  "Risk Flags": "No material off-balance-sheet risks or contingent liabilities identified."
}
```

Then enter a ticker (e.g., `TEST`) and share price (e.g., `100`), and click Analyze.

---

## Key Insights This Tool Reveals

- **Apple's 72% ROIC is real but misleading** — massive buybacks compressed equity to ~$74B. Return on Net Tangible Assets gives a truer picture of asset productivity.
- **Amazon's reported FCF understates real earnings** — headline FCF deducts all CapEx, but most is growth spending. Real profit strips out only maintenance.
- **SBC is the hidden tax** — many tech companies have SBC running 15-25% of CFO. Using grant value (not just expense) captures the full dilution.
- **Growth CapEx is only valuable if ROIC is high** — the Reinvestment Rate × ROIC tells you if growth spending is creating or destroying value.

---

## Sharing & Collaboration

- **Share the app**: Send anyone the [GitHub Pages link](https://lougualtieri-ami.github.io/real-economic-profit/) — it works in any browser
- **Share your data**: Use ⬇ Backup to export your portfolio as JSON, then others can ⬆ Restore it
- **Your data is private**: Portfolio data lives in your browser's localStorage — nobody else can see it
- **Feedback welcome**: Open an [Issue](https://github.com/lougualtieri-ami/real-economic-profit/issues) with suggestions, bugs, or companies that produce unexpected results

---

## Files

| File | Purpose |
|---|---|
| `index.html` | The complete app (single file, no dependencies beyond Google Fonts) |
| `gemini-extraction-guide_10K_6_.md` | Full extraction prompt + guide for pulling data from 10-K filings (includes risk scan) |
| `gemini-10Q-balance-sheet-prompt.md` | Prompt for updating balance sheet data from quarterly 10-Q filings |
| `claude-audit-prompt.md` | Prompt for Claude to cross-check extraction, explain the analysis, and flag concerns |

---

## Philosophy

Traditional financial analysis relies on metrics that companies can manipulate through accounting choices, buyback programs, and capitalization policies. This tool starts from cash — what actually flows through the business — and applies consistent adjustments to reveal the real economics.

The goal isn't to replace fundamental analysis. It's to give regular people a cleaner starting point — filtering long SEC filings so the important stuff comes out, interpreted correctly, and presented visually.

---

*Built with vanilla HTML/JS. No frameworks, no backend, no tracking. Your data stays in your browser.*
