# Claude Audit Prompt — Real Economic Profit Analyzer

> **Purpose:** Cross-verify a Gemini extraction against the source SEC filing, then generate a tailored Gemini research prompt for forward-looking analysis.
> **Version:** 3.5 — February 16, 2026

---

## When to Use

After Gemini extracts data from a 10-K or 10-Q filing. This prompt handles verification (Parts 1–5), auto-generates a customized Gemini research prompt, and — once the research comes back — completes the qualitative assessment (Part 6).

## Workflow

1. Start a **new Claude conversation** (one company per chat)
2. Name it clearly: e.g., **"META FY2024 Audit"**
3. **Upload the same 10-K or 10-Q filing** (PDF) that Gemini extracted from
4. Paste the **Prompt** below
5. Paste **Gemini's complete extraction response** (scratchpad + JSON)
6. Claude completes **Parts 1–5B as formatted text in chat** (no PDFs yet)
7. **Copy the generated prompt into Gemini Deep Research** and run it
8. **Paste the Gemini research report** back into the same Claude conversation
9. Claude completes **Part 6** and produces **two final PDF reports**

---

## Prompt

````
You are a senior financial analyst auditing a data extraction from an SEC filing. I'm providing:

1. THE GEMINI EXTRACTION — an AI's scratchpad (showing its work) and a JSON data block pulled from the attached SEC filing
2. THE ORIGINAL FILING — attached as a PDF

Please do the following:

PART 1 — DATA INTEGRITY CHECK
- **Verify the unit convention.** Check whether the filing reports in thousands, millions, or billions. All JSON values must be in MILLIONS ($M). If the filing reports in thousands, confirm Gemini divided by 1,000. This is a common source of errors for mid-cap and small-cap companies.
- Review Gemini's scratchpad reasoning. Did it identify the right line items from the right statements?
- Cross-check EVERY field in the JSON against the source filing. For each of the 27 fields:
  - Confirm the value matches the filing (after unit conversion if needed)
  - If wrong, state the correct value and where you found it
  - If null, confirm whether the data truly doesn't exist or if Gemini missed it
- Verify the SBC Grant Date Fair Value math (units × fair value, summed across award types)
- Verify Inventory/Receivables changes (correct YoY deltas, correct sign convention)
- Check that Depreciation excludes any impairment charges (use only recurring depreciation + depletion)
- Check that Total Shareholders Equity excludes noncontrolling / minority interest
- Check if the numbers look reasonable for this type of company (Does CFO make sense relative to Net Income? Is D&A reasonable relative to Total Assets? Does SBC Grant Value make sense relative to SBC Expense?)

PART 2 — FORMULA VERIFICATION
- Verify key calculations would work correctly: Real Economic Profit, Real ROIC, Invested Capital, Real Cash Yield
- Confirm the Maintenance vs Growth CapEx split makes sense for this business
- Check if the SBC treatment (MAX of expense vs grant value) is capturing the right number
- Flag any numbers that seem unusually high, low, or missing

PART 3 — WHAT AM I LOOKING AT? (Plain English)
Explain what this data tells me about the company as if I'm an intelligent person who is NOT a financial analyst. Specifically:
- Is this company generating real cash for shareholders, or are the numbers misleading?
- Is the growth spending creating value or destroying it?
- What are the 2-3 most important things I should take away?
- What should I watch for going forward?

PART 4 — RISK FLAGS
- Review any Risk Flags from the Gemini extraction. Are they material?
- Are there risks you can infer from the numbers that Gemini may not have flagged? (e.g., rapidly growing SBC, declining ROIC, debt growing faster than earnings, R&D intensity vs CapEx)

PART 5 — VERDICT & CORRECTED JSON
Provide a final verdict:
- ✅ CLEAN — all values verified correct
- ⚠️ MINOR ISSUES — list corrections needed
- ❌ MATERIAL ERRORS — list fields that need re-extraction

If ANY corrections are needed, output a corrected JSON block I can copy directly into the app.

**When the Conservative and Asset-Light views differ significantly** (opposite signs, or gap exceeds ~30% of CFO), show both sets of metrics side by side (Real Profit, ROIC, Yield) and note which view is more informative for this particular company.

PART 5B — GEMINI RESEARCH PROMPT (Auto-Generated)
After completing Parts 1-5, generate a customized Gemini Deep Research prompt that I can copy-paste directly into Gemini. The prompt must:
- Be filled in with ALL company-specific numbers from your audit (Real Profit, CFO, ROIC, Revenue, Yield, P/E, CapEx, R&D, SBC, risk flags)
- Include the observed Profit Momentum trends from multi-year data (profit trend, ROIC trend, revenue trend, invested capital trend)
- Set the research start date to the last day of the filing period you just audited
- Set the research end date to today
- Ask Gemini to research: (1) post-filing developments, (2) profit momentum drivers — specifically pricing power, volume trends, efficiency programs, business mix shifts, investment channels, and one-time factors, (3) risk flag updates, (4) forward guidance and consensus, (5) investment assessment
- Be formatted as a clean, ready-to-paste block

Label this section clearly: **"COPY THIS INTO GEMINI DEEP RESEARCH:"**

OUTPUT FORMAT FOR PARTS 1–5B
Output Parts 1–5B as well-formatted text directly in the chat. Use clear headings, tables, and bullet points for readability. Do NOT generate PDF reports at this stage — save the PDF generation for after Part 6 is complete to avoid doing it twice.

PART 6 — GEMINI RESEARCH ASSESSMENT (Complete when research is provided)
If a Gemini Deep Research report is provided along with or after the extraction data:
- Grade the research quality (Excellent / Good / Fair / Poor)
- Verify key factual claims against available data
- Note any post-filing developments that materially affect the analysis
- Flag any claims that appear unsourced or over-editorialized

Then, using BOTH the quantitative audit and the qualitative research, provide:
- **Profit Momentum Assessment:** What is actually driving the profit trend? (Pricing power? Volume? Efficiency? Mix shift? One-time items?) Be specific — use the Gemini research to explain the "why" behind the numbers.
- **Growth Quality Assessment:** Is the company investing effectively for future growth? Through what channels? Is the investment translating to results?
- **Forward Outlook:** Based on guidance, consensus, and qualitative context, is the profit momentum likely to continue, accelerate, or reverse?
- **The single most important insight** for an investment decision

Be direct and specific. Use the actual numbers. Don't hedge excessively — if something looks good or bad, say so clearly.

OUTPUT FORMAT — Two Downloadable PDF Reports (Generate ONLY after Part 6 is complete)

Produce two separate, well-formatted PDF reports containing ALL Parts (1–6):

**Report 1: Full Audit (Parts 1–6)** — For internal use.
Contains all parts including field-by-field verification tables, formula checks, analysis, risk flags, verdict, the generated Gemini prompt, and research assessment.

**Report 2: Shareable Analysis (Parts 3–6)** — For sharing externally.
Contains only the plain-English analysis (Part 3), risk flags (Part 4), a brief data verification summary (Part 5), and the research assessment (Part 6). Written to stand alone without raw data tables.

Both reports must include:
- A **header banner** with 4 key metrics (Real Profit, Real ROIC, Real Yield, and Verdict/Real P/E) displayed in colored boxes
- Professional formatting with clear Part headings, styled tables, and bullet points
- A footer with generation date, source filing, and share price

**Color Scheme (match exactly):**
- Green accent (#26ae60) — banner metric boxes, pass/clean indicators, divider lines
- Dark blue-gray (#2b3d4f) — secondary banner boxes
- Amber/gold (#f29c11) — yield/warning banner box
- Dark navy (#1a2331) — title text, part headings, table header backgrounds
- Table alt row (#edf2f6) — alternating table row shading
- Gray (#7e8c8c) — subtitles, footer text
- White text on colored backgrounds

**Typography:** Helvetica family. Title: 22pt Bold. Part headings: 16pt Bold. Subheadings: 12pt Bold. Body: 9.5pt. Table cells: 9pt.
````

---

## Tips

- **One company per conversation** — makes it easy to search later
- **Upload the same filing** — Claude needs the source document to verify against
- **Paste the full Gemini response** — the scratchpad gives Claude context about where Gemini found each number
- **Two-step process** — Claude will generate the Gemini research prompt in Part 5B. Copy it, run it in Gemini, paste the result back.
- **If Claude finds errors** — it produces a corrected JSON you can paste straight into the app
- **Multi-year is better** — if you're auditing multiple years, do them in the same conversation so Claude can spot cross-year patterns
- **Ask follow-ups** — after the audit, ask things like "How does this compare to a typical company in this sector?" or "Which single metric should I watch most closely?"
- **Compare two companies** — paste briefs for two companies and ask "Which is the better business and why?"
