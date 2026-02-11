# Claude Audit Prompt — Real Economic Profit Analyzer

## When to Use

After you've extracted data with Gemini, run the analysis in the app, and exported the brief. Paste both into Claude for a second-opinion review.

## What to Paste into Claude

1. **The Export Brief** from the app (click "Export Analysis Brief" → copy the contents)
2. **The Gemini extraction work** — the JSON output AND ideally the "show your work" response where Gemini explains where it found each number. (Copy from your Google Doc for that filing.)

## Prompt

Paste the Export Brief and Gemini work, then add this:

```
You are a senior financial analyst auditing a "Real Economic Profit" analysis. I'm providing two things:

1. AN EXPORT BRIEF from my analysis app (the computed results)
2. THE GEMINI EXTRACTION showing the raw data pulled from SEC filings and how it was derived

Please do the following:

PART 1 — DATA INTEGRITY CHECK
- Cross-check each number in the Export Brief against the Gemini extraction. Flag any mismatches.
- Check if the Gemini extraction looks reasonable for this type of company. For example: Does CFO make sense relative to Net Income? Is D&A in a reasonable range relative to Total Assets? Does SBC Grant Value make sense relative to SBC Expense?
- Flag any numbers that seem unusually high, low, or missing.

PART 2 — FORMULA VERIFICATION
- Verify the key calculations: Real Economic Profit, Real ROIC, Invested Capital, Real Cash Yield.
- Confirm the Maintenance vs Growth CapEx split makes sense for this business.
- Check if the SBC treatment (MAX of expense vs grant value) is capturing the right number.

PART 3 — WHAT AM I LOOKING AT? (Plain English)
Explain what this analysis tells me about the company as if I'm an intelligent person who is NOT a financial analyst. Specifically:
- Is this company generating real cash for shareholders, or are the numbers misleading?
- Is the growth spending creating value or destroying it?
- What are the 2-3 most important things I should take away?
- What should I watch for going forward?

PART 4 — RISK FLAGS
- Review any Risk Flags from the Gemini extraction. Are they material? What should I understand about them?
- Are there any risks you can infer from the numbers that Gemini may not have flagged? (e.g., rapidly growing SBC, declining ROIC, debt growing faster than earnings)

PART 5 — QUESTIONS I SHOULD ASK
- What follow-up questions would a professional analyst ask about this company based on this data?
- Is there anything in the numbers that doesn't add up or deserves a deeper look?

Be direct and specific. Use the actual numbers from the analysis. Don't hedge excessively — if something looks good or bad, say so clearly.
```

## Tips

- **One company at a time** — paste the brief and Gemini work for a single company per Claude conversation for the cleanest results.
- **Multi-year is better** — if you have the Export Brief from a multi-year analysis (with trend data), Claude can spot patterns like declining ROIC or accelerating SBC that single-year analysis misses.
- **Ask follow-ups** — after the audit, ask Claude things like "How does this compare to a typical company in this sector?" or "If I could only look at one metric to decide whether to own this stock, which would it be and why?"
- **Compare two companies** — paste Export Briefs for two companies and ask "Which is the better business and why?"
