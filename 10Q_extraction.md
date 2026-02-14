# 10-Q Data Extraction Guide — Real Economic Profit Analyzer

> **Purpose:** Extract interim financial data from SEC 10-Q filings for use in the [Real Economic Profit Analyzer](https://yourusername.github.io/real-economic-profit/).
> **Model Target:** Gemini (or any LLM with document upload capability)
> **Version:** 3.0 — February 14, 2026

---

## How to Use This Prompt

1. Upload the company's 10-Q filing (PDF or HTML) to Gemini
2. Paste the **Extraction Prompt** below into the chat
3. Gemini will respond with a **Scratchpad** (showing its work) followed by the **JSON**
4. Copy the **entire response** into a Claude audit chat for verification
5. Copy just the **JSON block** into the Real Economic Profit Analyzer app

One prompt. One response. No follow-up needed.

---

## Important: 10-Q vs 10-K Differences

- 10-Q reports are **quarterly** and cover a **year-to-date (YTD)** period. Use the **YTD cumulative** figures, not the single-quarter column.
- For Q3 filings (9 months), the YTD figures are the best available proxy for the full fiscal year until the 10-K is filed.
- **SBC Grant Date Fair Value** is rarely available in 10-Qs (abbreviated footnotes). Use null — the app falls back to SBC Expense.
- **Supplemental cash flow items** (Interest Paid, Income Tax Paid) may not appear in 10-Qs. Use null if not disclosed.
- **Balance sheet items** (debt, cash, equity, assets, goodwill, intangibles, shares) reflect the **quarter-end snapshot**, not YTD.

---

## Extraction Prompt

Copy everything inside the fence below and paste it into Gemini along with the uploaded 10-Q:

````
You are a financial data extraction analyst. Extract the following fields from this 10-Q filing. Use the YEAR-TO-DATE (YTD) cumulative figures for all flow items (revenue, cash flows, expenses). Use quarter-end balances for all stock items (balance sheet).

**OUTPUT FORMAT — Two Sections:**

**SECTION 1: Analyst Scratchpad**
Before generating the JSON, show your work in a scratchpad section. You MUST:
- State the fiscal quarter, period end date, and YTD period covered (e.g., "Q3 FY2025, period ending Sep 30, 2025, covering 9 months from Jan 1 to Sep 30")
- Confirm you are using the YTD column (not single-quarter) for all flow items
- State which cash flow statement lines you used for Cash From Operations, Total CapEx, D&A, and SBC Expense
- Note whether SBC Grant Date Fair Value data is available in the equity compensation footnote (usually it is not in 10-Qs)
- State the source for Depreciation if available (PP&E footnote or D&A breakout). If not broken out, state that and use null.
- Note whether supplemental cash flow disclosures (Interest Paid, Income Tax Paid) are present
- State the source for Inventory and Receivables changes (balance sheet deltas from fiscal year-end to quarter-end)
- Draft your Risk Flags summary

**SECTION 2: Final JSON**
After the scratchpad, output a single flat JSON object with exactly these 26 fields in this order. All dollar values in MILLIONS ($M). Use null ONLY if the data truly does not exist in the filing. Use 0 when the value is genuinely zero.

Fields:
1. "Fiscal Year Label" — Use the fiscal year label this quarter belongs to, e.g., "FY2025" for a Q3 filing in fiscal year 2025
2. "Fiscal Year End" — the quarter-end date, YYYY-MM-DD (NOT the fiscal year end)
3. "Cash From Operations" — YTD from cash flow statement
4. "SBC Expense addback" — YTD stock-based compensation from operating cash flows
5. "SBC Grant Date Fair Value" — null (typically not available in 10-Q footnotes)
6. "Total CapEx" — YTD purchases of PP&E (positive number)
7. "D&A" — YTD total depreciation and amortization
8. "Depreciation" — YTD depreciation only, excluding amortization of intangibles. null if not separately disclosed.
9. "Maintenance CapEx" — 0 (rarely disclosed in 10-Qs)
10. "Restructuring Charges" — YTD restructuring charges. 0 if none.
11. "Inventory Change" — change from fiscal year-end balance to quarter-end balance (positive = increase). null if not applicable.
12. "Receivables Change" — change from fiscal year-end balance to quarter-end balance (positive = increase)
13. "Interest Paid" — from supplemental cash flow disclosures. null if not disclosed in 10-Q.
14. "Income Tax Paid" — from supplemental cash flow disclosures. null if not disclosed in 10-Q.
15. "Revenue" — YTD total revenue
16. "Net Income" — YTD net income attributable to common shareholders
17. "Total Debt" — quarter-end sum of all borrowings (current + non-current)
18. "Cash & Equivalents" — quarter-end cash and cash equivalents
19. "Short-Term Investments" — quarter-end short-term marketable securities. 0 if none.
20. "Non-Current Marketable Securities" — quarter-end long-term marketable securities. 0 if none.
21. "Diluted Shares Outstanding" — YTD diluted weighted-average shares
22. "Total Shareholders Equity" — quarter-end total stockholders' equity
23. "Total Assets" — quarter-end total assets
24. "Goodwill" — quarter-end goodwill. 0 if none.
25. "Intangible Assets" — quarter-end intangible assets, net (excluding goodwill). 0 if none.
26. "Risk Flags" — 2-3 sentence summary of material risks from the 10-Q notes.
````

---

## Field Reference — Quick Lookup Table

| # | Field | 10-Q Source | Notes |
|---|-------|-------------|-------|
| 1 | Fiscal Year Label | Cover page | FY of the fiscal year, not quarter |
| 2 | Fiscal Year End | Cover page | Quarter-end date (NOT fiscal year end) |
| 3 | Cash From Operations | Cash flow statement | YTD cumulative |
| 4 | SBC Expense addback | Cash flow statement | YTD cumulative |
| 5 | SBC Grant Date Fair Value | — | Usually null for 10-Qs |
| 6 | Total CapEx | Cash flow statement | YTD, positive number |
| 7 | D&A | Cash flow statement | YTD cumulative |
| 8 | Depreciation | PP&E footnote | Often not broken out in 10-Qs |
| 9 | Maintenance CapEx | — | Use 0 |
| 10 | Restructuring Charges | Income statement / cash flow | YTD, 0 if none |
| 11 | Inventory Change | Balance sheet delta | Fiscal YE → quarter-end |
| 12 | Receivables Change | Balance sheet delta | Fiscal YE → quarter-end |
| 13 | Interest Paid | Supplemental cash flow | Often omitted in 10-Qs |
| 14 | Income Tax Paid | Supplemental cash flow | Often omitted in 10-Qs |
| 15 | Revenue | Income statement | YTD cumulative |
| 16 | Net Income | Income statement | YTD cumulative |
| 17 | Total Debt | Balance sheet | Quarter-end snapshot |
| 18 | Cash & Equivalents | Balance sheet | Quarter-end snapshot |
| 19 | Short-Term Investments | Balance sheet | Quarter-end snapshot |
| 20 | Non-Current Marketable Securities | Balance sheet | Quarter-end snapshot |
| 21 | Diluted Shares Outstanding | EPS section | YTD weighted-average |
| 22 | Total Shareholders Equity | Balance sheet | Quarter-end snapshot |
| 23 | Total Assets | Balance sheet | Quarter-end snapshot |
| 24 | Goodwill | Balance sheet | Quarter-end snapshot |
| 25 | Intangible Assets | Balance sheet | Quarter-end snapshot |
| 26 | Risk Flags | Notes | Material risks from 10-Q |

---

## Common 10-Q Pitfalls

- **YTD vs Single Quarter:** The cash flow statement and income statement in a 10-Q typically show both a single-quarter column and a YTD column. Always use YTD.
- **Q3 as Full-Year Proxy:** A Q3 10-Q covering 9 months is the best interim data available. The app treats it as the current year's data until the 10-K replaces it.
- **Balance Sheet Deltas:** Inventory and Receivables changes should be measured from the prior fiscal year-end (not prior quarter-end) to the current quarter-end.
- **Missing Supplemental Disclosures:** Many companies omit Interest Paid and Income Tax Paid from 10-Q cash flow statements. This is normal — use null.

---

## After Extraction

Paste Gemini's complete response (scratchpad + JSON) into a Claude conversation using the [Audit Prompt](CLAUDE_AUDIT_PROMPT.md) for cross-verification. Then import the JSON into the app.
