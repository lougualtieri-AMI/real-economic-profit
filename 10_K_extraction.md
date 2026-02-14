# 10-K Data Extraction Guide — Real Economic Profit Analyzer

> **Purpose:** Extract financial data from SEC 10-K filings for use in the [Real Economic Profit Analyzer](https://yourusername.github.io/real-economic-profit/).
> **Model Target:** Gemini (or any LLM with document upload capability)
> **Version:** 3.1 — February 14, 2026

---

## How to Use This Prompt

1. Upload the company's 10-K filing (PDF or HTML) to Gemini
2. Paste the **Extraction Prompt** below into the chat
3. Gemini will respond with a **Scratchpad** (showing its work) followed by the **JSON**
4. Copy the **entire response** into a Claude audit chat for verification
5. Copy just the **JSON block** into the Real Economic Profit Analyzer app

One prompt. One response. No follow-up needed.

---

## Extraction Prompt

Copy everything inside the fence below and paste it into Gemini along with the uploaded 10-K:

````
You are a financial data extraction analyst. Extract the following fields from this 10-K filing.

**OUTPUT FORMAT — Two Sections:**

**SECTION 1: Analyst Scratchpad**
Before generating the JSON, show your work in a scratchpad section. You MUST:
- State the fiscal year end date and confirm it matches the filing
- Show your math for SBC Grant Date Fair Value: find the equity compensation footnote, identify each award type (RSUs, PSUs, options, etc.), extract Units Granted × Weighted-Average Grant Date Fair Value for each, and sum them. If the footnote does not contain a grant date fair value table, state that clearly and use null.
- State which cash flow statement lines you used for Cash From Operations, Total CapEx, D&A, and SBC Expense
- State the source for Depreciation (usually in the PP&E footnote or D&A breakout). If D&A is not broken out, state that and use null.
- Show how you determined Maintenance CapEx (if disclosed; use 0 if not separately disclosed)
- State which supplemental cash flow lines you used for Interest Paid and Income Tax Paid
- Note the source for Inventory Change and Receivables Change (balance sheet year-over-year delta; positive = increase = cash use; report the CHANGE not the balance)
- State the R&D Expense from the income statement (usually "Research and development" or "Research, development and engineering")
- Draft your Risk Flags summary from Commitments & Contingencies, off-balance-sheet arrangements, and significant debt/lease obligations

**SECTION 2: Final JSON**
After the scratchpad, output a single flat JSON object with exactly these 27 fields in this order. All dollar values in MILLIONS ($M). Use null ONLY if the data truly does not exist in the filing after thorough checking. Use 0 when the value is genuinely zero.

Fields:
1. "Fiscal Year Label" — e.g., "FY2024"
2. "Fiscal Year End" — YYYY-MM-DD
3. "Cash From Operations" — from cash flow statement
4. "SBC Expense addback" — stock-based compensation expense added back in operating cash flows
5. "SBC Grant Date Fair Value" — Units Granted × Grant Date Fair Value, summed across all award types (from equity compensation footnote). null if no grant table exists.
6. "Total CapEx" — purchases of property, plant & equipment (positive number)
7. "D&A" — total depreciation and amortization (from cash flow statement or income statement)
8. "Depreciation" — depreciation only, excluding amortization of intangibles (from PP&E footnote or D&A breakout). null if not separately disclosed.
9. "Maintenance CapEx" — separately disclosed maintenance/sustaining capex. 0 if not disclosed.
10. "Restructuring Charges" — from income statement or cash flow adjustments. 0 if none.
11. "Inventory Change" — year-over-year change in inventory (positive = increase). null if not applicable (e.g., service companies).
12. "Receivables Change" — year-over-year change in accounts receivable (positive = increase)
13. "Interest Paid" — from supplemental cash flow disclosures
14. "Income Tax Paid" — from supplemental cash flow disclosures
15. "Revenue" — total revenue / net revenue
16. "Net Income" — net income attributable to common shareholders
17. "Total Debt" — sum of short-term borrowings + current portion of long-term debt + long-term debt
18. "Cash & Equivalents" — cash and cash equivalents
19. "Short-Term Investments" — short-term marketable securities. 0 if none.
20. "Non-Current Marketable Securities" — long-term investments / marketable securities. 0 if none.
21. "Diluted Shares Outstanding" — diluted weighted-average shares (from EPS footnote or income statement)
22. "Total Shareholders Equity" — total stockholders' equity
23. "Total Assets" — total assets from balance sheet
24. "Goodwill" — goodwill from balance sheet. 0 if none.
25. "Intangible Assets" — intangible assets, net (excluding goodwill). 0 if none.
26. "Risk Flags" — 2-3 sentence summary of material risks: off-balance-sheet items, contingent liabilities, unconsolidated entities, unusual debt structures, pending litigation with quantified exposure. State "None identified beyond standard operational risks" if nothing material.
27. "R&D Expense" — total research and development expense from the income statement. 0 if none (e.g., financial companies). null only if not separately disclosed.
````

---

## Field Reference — Quick Lookup Table

| # | Field | Where to Find It | Notes |
|---|-------|-------------------|-------|
| 1 | Fiscal Year Label | Cover page | FY + calendar year |
| 2 | Fiscal Year End | Cover page | YYYY-MM-DD |
| 3 | Cash From Operations | Cash flow statement | "Net cash provided by operating activities" |
| 4 | SBC Expense addback | Cash flow statement | Non-cash adjustment in operating section |
| 5 | SBC Grant Date Fair Value | Equity compensation footnote | Units × Fair Value per unit, summed |
| 6 | Total CapEx | Cash flow statement | "Purchases of PP&E" (positive number) |
| 7 | D&A | Cash flow statement | Total depreciation & amortization |
| 8 | Depreciation | PP&E footnote or D&A breakout | Depreciation only, no intangible amortization |
| 9 | Maintenance CapEx | MD&A or capex footnote | Rarely disclosed; use 0 |
| 10 | Restructuring Charges | Income statement / cash flow | 0 if none |
| 11 | Inventory Change | Balance sheet (YoY delta) | Positive = increase = cash use |
| 12 | Receivables Change | Balance sheet (YoY delta) | Positive = increase = cash use |
| 13 | Interest Paid | Supplemental cash flow | Near bottom of cash flow statement |
| 14 | Income Tax Paid | Supplemental cash flow | Near bottom of cash flow statement |
| 15 | Revenue | Income statement | Top line |
| 16 | Net Income | Income statement | Bottom line, attributable to common |
| 17 | Total Debt | Balance sheet | All borrowings, current + non-current |
| 18 | Cash & Equivalents | Balance sheet | Cash and cash equivalents |
| 19 | Short-Term Investments | Balance sheet | Marketable securities, current |
| 20 | Non-Current Marketable Securities | Balance sheet | Marketable securities, non-current |
| 21 | Diluted Shares Outstanding | EPS section | Weighted-average diluted |
| 22 | Total Shareholders Equity | Balance sheet | Total stockholders' equity |
| 23 | Total Assets | Balance sheet | Total assets |
| 24 | Goodwill | Balance sheet | Goodwill line item |
| 25 | Intangible Assets | Balance sheet | Net of accumulated amortization, ex-goodwill |
| 26 | Risk Flags | Notes to financial statements | Commitments, contingencies, off-BS items |
| 27 | R&D Expense | Income statement | "Research and development" line item |

---

## Common Extraction Pitfalls

- **SBC Grant Value vs SBC Expense:** These are different numbers. Expense = accounting charge recognized over vesting period. Grant Value = economic cost to shareholders in the grant year. The app uses both.
- **D&A vs Depreciation:** D&A includes amortization of acquired intangibles. Depreciation is physical asset wear only. The difference matters enormously for acquisitive companies (e.g., Broadcom, IBM).
- **CapEx sign convention:** Report as a positive number even though the cash flow statement shows it negative.
- **Inventory/Receivables:** Report the year-over-year CHANGE, not the balance. Positive = increase = cash consumed.
- **Total Debt:** Include ALL borrowings — commercial paper, revolving credit, current maturities of LT debt, and long-term debt.
- **Shares Outstanding:** Use the DILUTED weighted-average from the EPS calculation, not basic shares or shares outstanding at period end.
- **R&D Expense:** Use the total from the income statement. Some companies label it "Research and development," others "Research, development and engineering." Do NOT use capitalized software costs — use the expensed amount only.

---

## After Extraction

Paste Gemini's complete response (scratchpad + JSON) into a Claude conversation using the [Audit Prompt](CLAUDE_AUDIT_PROMPT.md) for cross-verification. Then import the JSON into the app.
