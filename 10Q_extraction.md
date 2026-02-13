# Real Economic Profit Analyzer — 10-Q Interim Extraction Guide

## Purpose

Use this prompt between annual 10-K filings to get a **current-year run rate** from quarterly filings. The app will annualize YTD cash flow items and use the updated balance sheet to give you a directional read on Real Economic Profit without waiting for the 10-K.

### How It Works

- **Cash flow items** are reported YTD in a 10-Q (e.g., Q2 = 6 months). The app annualizes them: `YTD value ÷ quarters elapsed × 4`
- **Balance sheet items** are point-in-time snapshots — no annualization needed
- **SBC Grant Value** may not be calculable from a 10-Q (thinner notes) — return what's available, the app falls back to SBC Expense
- Fields not available in the 10-Q import as `null` and the app carries forward the last 10-K value where appropriate

### When to Use

- **Q1, Q2, Q3 filings** — upload the 10-Q and use this prompt
- **Q4** — typically no 10-Q is filed; use the 6-K/99.1 prompt for earnings releases, then replace with the 10-K when filed
- **After importing**, the app flags the year as "Interim (10-Q)" so you know the data is approximate

---

## Extraction Prompt

Upload the company's **10-Q**. Then use this prompt:

```
Role: Senior Financial Analyst
Task: Extract financial data from this 10-Q filing for interim "Real Economic Profit" analysis. The app will annualize YTD cash flow items automatically.

IMPORTANT — PERIOD IDENTIFICATION:
First, identify the period covered by this filing:
- "Fiscal Year Label" — The fiscal year this quarter belongs to (e.g., "FY2025", "FY2026")
- "Fiscal Year End" — The expected fiscal year-end date in YYYY-MM-DD format
- "Quarter" — Which quarter: 1, 2, or 3
- "Quarter End Date" — The exact quarter-end date in YYYY-MM-DD format
- "Filing Type" — Return "10-Q"

CRITICAL — YTD vs QUARTERLY:
10-Q cash flow statements show YEAR-TO-DATE (YTD) figures, not single-quarter figures.
- For a Q2 filing: cash flow numbers cover 6 months (Jan–Jun or equivalent)
- For a Q3 filing: cash flow numbers cover 9 months (Jan–Sep or equivalent)
- ALWAYS return the YTD numbers. The app handles annualization.

OUTPUT: Single FLAT JSON object. All values in MILLIONS ($M). No nested objects. No comments. Use null if not found in the 10-Q.

PERIOD FIELDS:

1. "Fiscal Year Label" — e.g., "FY2025"
2. "Fiscal Year End" — Expected year-end as YYYY-MM-DD
3. "Quarter" — 1, 2, or 3
4. "Quarter End Date" — Exact quarter-end as YYYY-MM-DD
5. "Filing Type" — "10-Q"

CASH FLOW ITEMS (YTD from cash flow statement):

6. "Cash From Operations" — YTD net cash provided by operating activities
7. "SBC Expense addback" — YTD stock-based compensation added back in CFO section
8. "SBC Grant Date Fair Value" — If the SBC note provides units granted YTD and grant-date fair value, calculate and return. If not available in the 10-Q, return null. DO check the Stock Compensation note — some 10-Qs include a grant activity table.
9. "Total CapEx" — YTD purchases of property, plant and equipment (return as POSITIVE number)
10. "D&A" — YTD depreciation and amortization from cash flow statement
11. "Depreciation" — YTD depreciation of property/equipment ONLY, excluding amortization of intangible assets. Check Notes to PP&E if available. Return null if not broken out in the 10-Q.
12. "Restructuring Charges" — YTD from operating expenses or cash flow; 0 if none
13. "Interest Paid" — From Supplemental Disclosures if available; null if not found
14. "Income Tax Paid" — From Supplemental Disclosures if available; null if not found

INCOME STATEMENT (YTD):

15. "Revenue" — YTD total revenue / net revenue
16. "Net Income" — YTD net income attributable to common shareholders

BALANCE SHEET ITEMS (quarter-end snapshot — NOT annualized):

17. "Total Debt" — Sum: commercial paper + current term debt + non-current term debt
18. "Cash & Equivalents" — Cash and cash equivalents
19. "Short-Term Investments" — Current marketable securities; 0 if none
20. "Non-Current Marketable Securities" — Liquid bond portfolios only; 0 if none
21. "Diluted Shares Outstanding" — Weighted-average diluted from YTD EPS calculation
22. "Total Shareholders Equity" — Total stockholders' equity from balance sheet
23. "Total Assets" — Total assets from balance sheet
24. "Goodwill" — Goodwill from balance sheet; 0 if none
25. "Intangible Assets" — Net intangible assets (excluding goodwill); 0 if none

MAINTENANCE CAPEX (if disclosed):

26. "Maintenance CapEx" — Only if separately disclosed in MD&A; otherwise return 0
```

### Expected JSON Output

```json
{
  "Fiscal Year Label": "FY2025",
  "Fiscal Year End": "2025-12-31",
  "Quarter": 2,
  "Quarter End Date": "2025-06-30",
  "Filing Type": "10-Q",
  "Cash From Operations": 55000,
  "SBC Expense addback": 10200,
  "SBC Grant Date Fair Value": null,
  "Total CapEx": 28000,
  "D&A": 18500,
  "Depreciation": 14200,
  "Restructuring Charges": 0,
  "Interest Paid": 800,
  "Income Tax Paid": 5200,
  "Revenue": 180000,
  "Net Income": 42000,
  "Total Debt": 35000,
  "Cash & Equivalents": 28000,
  "Short-Term Investments": 15000,
  "Non-Current Marketable Securities": 0,
  "Diluted Shares Outstanding": 10500,
  "Total Shareholders Equity": 290000,
  "Total Assets": 480000,
  "Goodwill": 23000,
  "Intangible Assets": 8500,
  "Maintenance CapEx": 0
}
```

---

## Key Notes for 10-Q Extraction

### YTD Cash Flow — Do Not Annualize
Return the raw YTD numbers exactly as reported. The app divides by the quarter number and multiplies by 4 to get the annualized run rate. Getting this wrong would double-count.

### SBC Grant Value — Best Effort
10-Q notes are thinner than 10-K notes. Some companies include a grant activity table in their quarterly SBC note; others don't. If you can calculate it, do. If not, return null — the app will use SBC Expense instead (slightly less accurate but directionally correct).

### Depreciation — New Field
If the 10-Q breaks out depreciation vs. amortization (in the cash flow statement, PP&E note, or segment disclosures), return the depreciation-only figure. This enables the Asset-Light Maintenance CapEx view for acquisitive companies. Return null if not separately disclosed — the app handles the fallback.

### Balance Sheet — Current Quarter
Unlike cash flow items, balance sheet numbers are already a snapshot. No adjustment needed. These give you the freshest view of debt, cash, equity, and invested capital.

### What the App Does With This Data
1. Annualizes YTD cash flow: `annualized = YTD ÷ quarter × 4`
2. Uses quarter-end balance sheet as-is
3. Calculates Real Economic Profit on the annualized run rate
4. Flags the analysis as "Interim (Q[n] 10-Q)" in the UI and export
5. When you later import the full-year 10-K, it replaces the interim estimate

### Verification
Same as 10-K workflow: ask Gemini to show its work, run the analysis, export the brief, and audit with Claude. Note in the audit that this is an interim estimate based on YTD annualization.
