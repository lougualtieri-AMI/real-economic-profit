# Claude Audit Prompt — Real Economic Profit Analyzer

> **Purpose:** Cross-verify a Gemini extraction against the source SEC filing, then review the analysis.
> **Version:** 3.1 — February 14, 2026

---

## When to Use

After Gemini extracts data from a 10-K or 10-Q filing. This prompt handles everything in one step: verifying the raw numbers, checking the math, explaining the results, and flagging risks.

## How to Use

1. Start a **new Claude conversation** (one company per chat for searchability)
2. Name it clearly: e.g., **"META FY2024 Audit"** or **"AVGO FY2025 Audit"**
3. **Upload the same 10-K or 10-Q filing** (PDF) that Gemini extracted from
4. Paste the **Prompt** below
5. Paste **Gemini's complete response** (scratchpad + JSON) after it

---

## Prompt

````
You are a senior financial analyst auditing a data extraction from an SEC filing. I'm providing:

1. THE GEMINI EXTRACTION — an AI's scratchpad (showing its work) and a JSON data block pulled from the attached SEC filing
2. THE ORIGINAL FILING — attached as a PDF

Please do the following:

PART 1 — DATA INTEGRITY CHECK
- Review Gemini's scratchpad reasoning. Did it identify the right line items from the right statements?
- Cross-check EVERY field in the JSON against the source filing. For each of the 27 fields:
  - Confirm the value matches the filing
  - If wrong, state the correct value and where you found it
  - If null, confirm whether the data truly doesn't exist or if Gemini missed it
- Verify the SBC Grant Date Fair Value math (units × fair value, summed across award types)
- Verify Inventory/Receivables changes (correct YoY deltas, correct sign convention)
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

Be direct and specific. Use the actual numbers. Don't hedge excessively — if something looks good or bad, say so clearly.
````

---

## Tips

- **One company per conversation** — makes it easy to search later
- **Upload the same filing** — Claude needs the source document to verify against
- **Paste the full Gemini response** — the scratchpad gives Claude context about where Gemini found each number
- **If Claude finds errors** — it produces a corrected JSON you can paste straight into the app
- **Multi-year is better** — if you're auditing multiple years, do them in the same conversation so Claude can spot cross-year patterns
- **Ask follow-ups** — after the audit, ask things like "How does this compare to a typical company in this sector?" or "Which single metric should I watch most closely?"
- **Compare two companies** — paste briefs for two companies and ask "Which is the better business and why?"
