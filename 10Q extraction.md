# 10-Q Balance Sheet Extraction Prompt

## When to Use
Use this prompt when you have a company's **annual 10-K already imported** into the Real Economic Profit Analyzer and want to update the balance sheet with more current numbers from the latest **10-Q**.

**Important:** This only extracts balance sheet items. Cash flow items (CFO, CapEx, SBC, etc.) should always come from the annual 10-K.

## Prompt

Upload the company's latest **10-Q** filing to Gemini, then paste this:

```
Role: Senior Financial Analyst
Task: Extract BALANCE SHEET items only from this 10-Q filing.

These will be used to update an annual analysis that already has full-year cash flow data from the 10-K. I only need the most current balance sheet snapshot.

OUTPUT: Single FLAT JSON object. All values in MILLIONS ($M). No nested objects. No comments. Use null only if truly not found.

QUARTER IDENTIFICATION:

1. "Quarter Label" — e.g., "Q2 FY2025" or "Q3 FY2024"
2. "Quarter End Date" — Exact end date as YYYY-MM-DD
3. "Fiscal Year Label" — The fiscal year this quarter belongs to, e.g., "FY2025"

BALANCE SHEET ITEMS (from the quarter-end balance sheet):

4. "Total Debt" — Sum: commercial paper + current term debt + non-current term debt. Include finance lease obligations if material.
5. "Cash & Equivalents" — Cash and cash equivalents
6. "Short-Term Investments" — Current marketable securities; 0 if none
7. "Non-Current Marketable Securities" — Liquid bond portfolios only; EXCLUDE illiquid strategic/private equity investments; 0 if none
8. "Diluted Shares Outstanding" — Weighted-average diluted from EPS calculation (use the quarterly or YTD figure)
9. "Total Shareholders Equity" — Total stockholders'/shareholders' equity
10. "Total Assets" — Total assets
11. "Goodwill" — Goodwill from balance sheet; 0 if none
12. "Intangible Assets" — Net intangible assets (excluding goodwill); 0 if none. If combined with goodwill on the balance sheet, check the Notes for the breakout.

OPTIONAL (if easily found in the 10-Q):

13. "Revenue YTD" — Year-to-date revenue (useful for estimating full-year run rate); null if not clear
14. "Net Income YTD" — Year-to-date net income; null if not clear
```

## Expected Output

```json
{
  "Quarter Label": "Q2 FY2025",
  "Quarter End Date": "2024-12-27",
  "Fiscal Year Label": "FY2025",
  "Total Debt": 500,
  "Cash & Equivalents": 250,
  "Short-Term Investments": 100,
  "Non-Current Marketable Securities": 0,
  "Diluted Shares Outstanding": 37,
  "Total Shareholders Equity": 2800,
  "Total Assets": 4500,
  "Goodwill": 120,
  "Intangible Assets": 45,
  "Revenue YTD": 1500,
  "Net Income YTD": 200
}
```

## How to Use the Results

After Gemini returns the JSON:

1. Open the Real Economic Profit Analyzer
2. Load the company from your portfolio (or import the 10-K first)
3. **Manually update** these balance sheet fields in the form with the 10-Q numbers:
   - Total Debt
   - Cash & Equivalents
   - Short-Term Investments
   - Diluted Shares Outstanding
   - Total Shareholders' Equity
   - Total Assets
   - Goodwill
   - Intangible Assets
4. Non-Cash LT Investments (if applicable)
5. Hit **Analyze** with the updated balance sheet
6. **Save** to portfolio to keep the updated numbers
