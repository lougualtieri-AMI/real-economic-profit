# Real Economic Profit Analyzer — Gemini Extraction Guide

## What This App Does

The Real Economic Profit Analyzer calculates what a business actually earns in cash — cutting through accounting noise in SEC filings. It answers: **"What is the real cash yield on my investment, and is the company's growth spending creating value?"**

### The Core Formulas

```
Real Economic Profit = CFO − Real SBC (MAX of expense vs grant value) − Maintenance CapEx − Restructuring + Investment Income
Real Cash Yield = Real Economic Profit ÷ Enterprise Value
Real ROIC = Real Economic Profit ÷ Invested Capital (Equity + Debt − Cash)
Return on NTA = Real Economic Profit ÷ Net Tangible Assets (Total Assets − Goodwill − Intangibles − Cash − STI)
Growth CapEx = Total CapEx − Maintenance CapEx
Incremental Return = Δ Real Profit ÷ Prior Year Growth CapEx
```

---

## Multi-Year Workflow

The app supports **historical trend analysis** — import multiple years to see ROIC trajectory, profit trends, and capital efficiency over time.

### Steps:
1. **Use a separate Gemini chat for each 10-K** (avoids cross-year confusion)
2. Upload the 10-K, paste the prompt below, get JSON back
3. Paste each year's JSON into the app via "Import 10-K Data from Gemini"
4. The app auto-detects the fiscal year and accumulates data
5. After importing 2+ years, the Historical Trend section appears automatically

### For the most recent year:
- Upload **both** the 10-K (cash flow) AND latest 10-Q (balance sheet)
- Enter the current share price manually

### For older years:
- Upload just the 10-K — all items come from there
- Balance sheet items come from the 10-K year-end balance sheet

---

## Extraction Prompt

Upload the company's **10-K** (and optionally the **latest 10-Q** for the most recent year). Then use this prompt:

```
Role: Senior Financial Analyst
Task: Extract financial data from this filing for "Real Economic Profit" analysis.

IMPORTANT — FISCAL YEAR IDENTIFICATION:
First, identify the fiscal year covered by this filing and return these fields:
- "Fiscal Year Label" — The company's label (e.g., "FY2024", "FY2025", "FY2026")
- "Fiscal Year End" — The exact end date in YYYY-MM-DD format (e.g., "2024-12-31", "2025-01-28", "2024-09-28")

SOURCING RULES:
- All CASH FLOW items: Full-year numbers from the filing
- All BALANCE SHEET items: Year-end snapshot from the filing (or from 10-Q if provided)

OUTPUT: Single FLAT JSON object. All values in MILLIONS ($M). No nested objects. No comments. Use null only if truly not found.

FISCAL YEAR FIELDS:

1. "Fiscal Year Label" — e.g., "FY2024" or "FY2026"
2. "Fiscal Year End" — Exact end date as YYYY-MM-DD

CASH FLOW ITEMS (from annual filing):

3. "Cash From Operations" — Net cash provided by operating activities
4. "SBC Expense addback" — Stock-based compensation added back in CFO section
5. "SBC Grant Date Fair Value" — CRITICAL: DO NOT return null without checking. Go to the Stock-Based Compensation Note. Find RSUs (and/or PSUs/options) granted during the fiscal year. Multiply [Units Granted] × [Weighted-Average Grant Date Fair Value]. Sum all award types.
6. "Total CapEx" — Purchases of property, plant and equipment (return as POSITIVE number)
7. "D&A" — Depreciation and amortization from operating activities in cash flow statement
8. "Depreciation" — Depreciation of property/equipment ONLY, excluding amortization of intangible assets. Check the PP&E note or segment disclosures for the breakout. Return null if not separately disclosed in the filing.
9. "Maintenance CapEx" — Only if separately disclosed in MD&A; otherwise return 0
10. "Restructuring Charges" — From operating expenses or cash flow; 0 if none
11. "Inventory Change" — Positive = decrease (cash freed), Negative = increase (cash trapped); 0 if N/A
12. "Receivables Change" — Positive = decrease (cash collected), Negative = increase (cash not collected)
13. "Interest Paid" — From Supplemental Disclosures at bottom of Cash Flow Statement; null if not found
14. "Income Tax Paid" — From Supplemental Disclosures at bottom of Cash Flow Statement; null if not found

OPTIONAL INCOME STATEMENT (for trend analysis):

15. "Revenue" — Total revenue / net revenue for the fiscal year; null if not in filing
16. "Net Income" — Net income attributable to common shareholders; null if not in filing

BALANCE SHEET ITEMS (from latest available — 10-Q if provided, otherwise 10-K year-end):

17. "Total Debt" — Sum: commercial paper + current term debt + non-current term debt
18. "Cash & Equivalents" — Cash and cash equivalents
19. "Short-Term Investments" — Current marketable securities
20. "Non-Current Marketable Securities" — Liquid bond portfolios only; EXCLUDE illiquid strategic/private equity investments; 0 if none
21. "Diluted Shares Outstanding" — Weighted-average diluted from EPS calculation
22. "Total Shareholders Equity" — Total stockholders'/shareholders' equity from balance sheet
23. "Total Assets" — Total assets from balance sheet
24. "Goodwill" — Goodwill from balance sheet; 0 if none
25. "Intangible Assets" — Net intangible assets (excluding goodwill) from balance sheet; 0 if none. If combined with goodwill on the balance sheet, check the Notes for the breakout.

RISK & OFF-BALANCE-SHEET SCAN:

After extracting the numbers above, scan the filing's footnotes (especially Commitments & Contingencies, Variable Interest Entities, Related Party Transactions, and Subsequent Events) and return:

26. "Risk Flags" — A text string summarizing anything an investor should know about that is NOT captured in the numbers above. Specifically flag:
    - Off-balance-sheet arrangements (SPVs, VIEs, synthetic leases, securitizations)
    - Unconsolidated entities and the company's exposure to them
    - Contingent liabilities (guarantees, indemnifications, pending litigation with material exposure)
    - Material contractual commitments (purchase obligations, capital commitments, take-or-pay contracts)
    - Related-party transactions that could affect cash flows
    - Significant changes in accounting policy or restatements
    - Any unusual items that could materially affect future cash flows
    If nothing material found, return "No material off-balance-sheet risks or contingent liabilities identified."
```

### Expected JSON Output

```json
{
  "Fiscal Year Label": "FY2024",
  "Fiscal Year End": "2024-12-31",
  "Cash From Operations": 111482,
  "SBC Expense addback": 22785,
  "SBC Grant Date Fair Value": 21813,
  "Total CapEx": 52535,
  "D&A": 15311,
  "Depreciation": 12400,
  "Maintenance CapEx": 0,
  "Restructuring Charges": 0,
  "Inventory Change": 0,
  "Receivables Change": -4376,
  "Interest Paid": null,
  "Income Tax Paid": 43369,
  "Revenue": 350018,
  "Net Income": 100681,
  "Total Debt": 10883,
  "Cash & Equivalents": 23466,
  "Short-Term Investments": 72191,
  "Non-Current Marketable Securities": 0,
  "Diluted Shares Outstanding": 12452,
  "Total Shareholders Equity": 315517,
  "Total Assets": 450243,
  "Goodwill": 32535,
  "Intangible Assets": 3223,
  "Risk Flags": "No material off-balance-sheet risks or contingent liabilities identified."
}
```

---

## Key Extraction Notes

### Fiscal Year (Required for Multi-Year)
The app uses these fields to auto-slot data chronologically. NVIDIA's FY2026 ends Jan 2025; Apple's FY2024 ends Sep 2024. Always use the company's own label and the actual calendar end date.

### SBC Grant Value (Most Critical)
Go to the Stock-Based Compensation Note and calculate: units granted × weighted-average grant-date fair value. If there are RSUs + PSUs + options, sum all. The app uses MAX(Expense, Grant Value) to capture the true dilution cost.

### CapEx Sign Convention
Return CapEx as a **positive number**. The app subtracts it internally.

### D&A (Do Not Skip)
Essential for the Maintenance CapEx auto-estimate. If CapEx >> D&A, the app treats only D&A × 1.05 as maintenance, with the rest classified as growth spending.

### Depreciation (New — Enables Asset-Light View)
If the 10-K breaks out depreciation vs. amortization — in the cash flow statement, PP&E note, or segment disclosures — return the depreciation-only figure. This enables the Asset-Light Maintenance CapEx view for acquisitive companies (Broadcom, Google, LLY), where amortization of acquired intangibles inflates D&A but doesn't represent real cash maintenance needs. Return null if not separately disclosed — the app handles the fallback to full D&A.

### Total Debt
Must include ALL debt: commercial paper + current maturities + non-current long-term debt. Include finance lease obligations if material.

### Non-Current Marketable Securities
Only include liquid, fixed-income portfolios. Exclude strategic investments, private equity, venture stakes.

### Goodwill & Intangible Assets
These enable the **Return on NTA** metric, which strips out buyback distortion and acquisition premium to show what the operating assets actually earn. Companies with heavy buybacks (Apple) will show a very different ROIC vs Return on NTA. Companies with large acquisitions (Broadcom, Google) will show how much of their asset base is goodwill vs productive tangible assets. If "Goodwill and intangible assets, net" is a single line, check the Notes for the breakout.

### Risk Flags
This is a plain-text summary — not a number. The app stores it for reference and displays it in the analysis. The goal is to catch things that don't show up in the financial statements: hidden leverage through SPVs, guarantees that could become real liabilities, litigation that could drain cash, and contractual commitments that lock in future spending. Even if the flag says "nothing material," that's useful confirmation.

### Revenue & Net Income (Optional)
These enhance the trend table but are not required for the core analysis. Include them when available for richer historical comparison.

### Supplemental Disclosures
Interest Paid and Income Tax Paid are almost always at the bottom of the Cash Flow Statement. Cash taxes paid can be dramatically different from the tax provision.

---

## Verification Workflow

After extracting data, the recommended audit process is:

1. **Ask Gemini to show its work** — In the same chat, ask: "Show me where you found each number and how you calculated it." Save this to a Google Doc for each filing.
2. **Run the analysis** in the app and hit **Export Brief**
3. **Audit with Claude** — Paste both the Export Brief and Gemini's work into Claude using the audit prompt (see `claude-audit-prompt.md`). Claude cross-checks the extraction against the analysis and explains what you're looking at in plain English.

---

## Suggestions Welcome

If you notice anything in the filings relevant to real economic profit that isn't captured — such as unusual non-cash charges, large acquisition-related amortization, or hidden liabilities — please flag it alongside the JSON.
