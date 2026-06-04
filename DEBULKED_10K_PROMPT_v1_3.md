



# Debulked 10-K Prompt — v1.3
# Purpose: Pre-process a full 10-K (or 10-Q) through NotebookLM to extract only the financial sections REPA needs. Output is a ~30-page distilled source file that can replace a 150+ page filing as input to the Claude audit, reducing token load and improving section coverage.
# v1.3: Hardened the 10-Q path — Section 11 now names the Part II, Item 2 "Issuer Purchases of Equity Securities" monthly table (the 10-Q source for quarterly buyback share counts); added a 10-Q note on condensed/unaudited statement titles and the prior same-quarter comparative column the TTM build requires.
# v1.2: Applied Field Dictionary Item #1 to Section 8 — broadened to require non-marketable strategic equity investments (equity-method holdings, measurement-alternative private-company stakes), not just marketable securities. Closes the pre-process half of the NVDA gap so the broadened extraction field has a source to read.
# v1.1: Added Section 11 (Share Repurchases & Dividends) to support extraction prompt fields 28-31. Tightened Section 1 to require supplemental cash flow disclosures. Added explicit diluted shares and marketable securities requirements.

---

Extract the following sections **verbatim** from this filing. Do not summarize, paraphrase, or omit any rows, line items, or footnote language. Preserve all dollar amounts, dates, and table formatting exactly as they appear. For each section, include the **page number(s)** and **section heading** as shown in the filing.

Output as plain text with clear section dividers.

**If this filing is a 10-Q:** its statements are titled "Condensed Consolidated Statements of…" and are unaudited — extract them the same way (the title wording differs from a 10-K's, that's expected). "All periods presented" in the statement sections **must include the prior-year comparative column** (e.g., the "Six Months Ended [prior year]" column) — the TTM build depends on that prior same-quarter YTD figure, so do not drop it. Section 10 (Critical Accounting Estimates) is 10-K only; skip it for a 10-Q.

### SECTION 1: CONSOLIDATED STATEMENTS OF CASH FLOWS
- Extract the **complete** Consolidated Statements of Cash Flows for all periods presented
- Include every line item — operating, investing, and financing activities
- **Required**: Include supplemental cash flow disclosures (interest paid, income taxes paid, non-cash investing/financing activities). These are typically in a separate table immediately following the main cash flow statement.

### SECTION 2: CONSOLIDATED STATEMENTS OF OPERATIONS (INCOME STATEMENT)
- Extract the **complete** Consolidated Statements of Operations for all periods presented
- Include every line item from revenue through net income
- Include per-share data (basic and diluted EPS)
- **Required**: Include weighted-average shares outstanding (basic AND diluted) for all periods presented. These are typically shown directly below the EPS lines.

### SECTION 3: CONSOLIDATED BALANCE SHEET
- Extract the **complete** Consolidated Balance Sheet as of all dates presented
- Include every line item — assets, liabilities, and stockholders' equity
- Include parenthetical details (par value, shares authorized/outstanding)

### SECTION 4: STOCK-BASED COMPENSATION (FULL FOOTNOTE)
- Extract the **entire** footnote on stock-based compensation / share-based payments
- Include: expense recognized by type (RSUs, options, ESPP, PSUs), grant-date fair value tables, vesting schedules, unrecognized compensation and weighted-average recognition period
- Include any tables showing units/shares granted, vested, forfeited, and outstanding
- If SBC is discussed across multiple footnotes (e.g., "Employee Benefit Plans"), extract all relevant sections
- **CRITICAL**: Include any language about acquisition-related equity awards, retention grants, or one-time SBC charges

### SECTION 5: PROPERTY, EQUIPMENT & DEPRECIATION / AMORTIZATION
- Extract the **complete** Property, Plant & Equipment footnote including useful life ranges and accumulated depreciation
- Extract the **complete** Goodwill and Intangible Assets footnote including amortization schedules, useful lives, and accumulated amortization
- If depreciation and amortization amounts are disclosed in a separate footnote or in the segment discussion, extract those as well
- Include any tables showing D&A by category or segment

### SECTION 6: RESTRUCTURING, IMPAIRMENT & SPECIAL CHARGES
- Extract **all** footnotes or disclosures related to restructuring charges, impairment charges, asset write-downs, or exit/disposal activities
- Include: charge amounts, accrual balances, expected remaining charges, and timeline
- If the filing states there are NO restructuring or impairment charges, note that explicitly
- Include any acquisition-related integration costs if disclosed separately

### SECTION 7: REVENUE RECOGNITION & SEGMENT INFORMATION
- Extract the **complete** Revenue Recognition footnote (ASC 606 disclosures)
- Include disaggregated revenue tables (by geography, product/service, timing of recognition)
- Extract the **complete** Segment Information footnote including segment revenue, operating income, and any segment-level D&A or CapEx disclosures

### SECTION 8: DEBT, FINANCING, MARKETABLE SECURITIES & STRATEGIC EQUITY
- Extract the **complete** Debt/Borrowings footnote
- Include: outstanding balances, interest rates, maturity dates, credit facility terms
- Include short-term borrowings and commercial paper if applicable
- **Required**: Extract the **complete** Marketable Securities / Investments footnote (often a separate note covering available-for-sale securities, equity investments, and trading securities). Include the breakdown between current (short-term) and non-current (long-term) holdings, by category.
- **Required**: Extract any footnote or balance-sheet line covering **non-marketable strategic equity investments** — equity-method holdings, measurement-alternative (privately held / non-marketable) equity securities, and other long-term strategic stakes in private companies. These are frequently disclosed *separately* from the marketable-securities note (often under "Investments," "Equity Investments," or within "Other assets") and are easy to miss. Include carrying value, the balance-sheet line where they sit, and any fair-value adjustments or unrealized gains/losses recognized in the period. If the filing discloses no such holdings, state that explicitly.

### SECTION 9: COMMITMENTS, CONTINGENCIES & LEASES (BRIEF)
- Extract the operating lease right-of-use asset and liability balances
- Extract future minimum lease payment schedule
- Note any material litigation or contingent liabilities (brief summary acceptable here)

### SECTION 10: CRITICAL ACCOUNTING ESTIMATES (10-K ONLY)
- Extract the Critical Accounting Estimates section from MD&A (if 10-K)
- This often contains management's key judgments on impairment testing, revenue recognition thresholds, and SBC valuation assumptions

### SECTION 11: SHARE REPURCHASES & DIVIDENDS
- Extract the **complete** Stockholders' Equity footnote (or equivalent — sometimes titled "Common Stock," "Capital Stock," or "Share Repurchase Program")
- **Required**: Total shares repurchased and total cash spent on repurchases for each period presented (in both the footnote and the Statement of Cash Flows financing section)
- **10-Q only**: also extract the **Part II, Item 2 "Issuer Purchases of Equity Securities"** table (the monthly share-repurchase table) — for a 10-Q this is the primary source for the quarterly buyback **share count**, which the Stockholders' Equity footnote often omits. Include the per-month shares purchased and average price paid.
- Include average price paid per share if disclosed
- Include accelerated share repurchase (ASR) program details if applicable, including share counts settled in each period
- Include any excise tax on repurchases disclosed separately
- **Required**: Dividends declared per share AND total cash dividends paid for each period presented
- If the filing states there were NO repurchases or NO dividends, note that explicitly

---

**FORMAT INSTRUCTIONS:**
- Begin each section with: `=== SECTION [#]: [TITLE] === (Pages [X]-[Y])`
- Preserve all numerical tables using aligned columns or markdown table format
- Use `[No disclosure found]` if a section does not exist in this filing
- Do NOT add any analysis, commentary, or interpretation — extract only
