# Claude Audit Prompt — Real Economic Profit Analyzer

> **Purpose:** Cross-verify a Gemini extraction against the source SEC filing before importing into the app.
> **Version:** 3.1 — February 14, 2026

---

## How to Use

1. Start a new Claude conversation (one company per chat for searchability)
2. Upload the same 10-K or 10-Q filing (PDF) that Gemini extracted from
3. Paste Gemini's **complete response** (scratchpad + JSON) below this prompt
4. Claude will verify every number against the source document

Name the conversation clearly for future reference, e.g., **"AVGO FY2025 Audit"** or **"META FY2024 Audit"**

---

## Audit Prompt

Paste this into Claude, then paste Gemini's full response after it:

````
You are a financial data auditor. I'm going to give you a Gemini AI extraction of an SEC filing. The extraction includes a scratchpad (showing Gemini's work) and a JSON data block. The original filing is attached as a PDF.

Please perform the following audit:

**1. Scratchpad Review**
- Does Gemini's scratchpad reasoning look correct?
- Did it identify the right line items from the right statements?
- Is the SBC Grant Date Fair Value math correct (units × fair value, summed across award types)?
- Are the Inventory/Receivables changes calculated correctly (YoY deltas, correct sign convention)?

**2. JSON Verification — Check Every Field**
For each of the 27 fields in the JSON:
- Confirm the value matches the source filing
- If a value is wrong, state the correct value and where you found it
- If a value is null, confirm whether the data truly doesn't exist in the filing or if Gemini missed it

**3. Risk Flags Assessment**
- Are there material risks Gemini missed?
- Is anything overstated or understated?

**4. Summary**
Provide a final verdict:
- ✅ CLEAN — all values verified correct
- ⚠️ MINOR ISSUES — list corrections needed (typos, rounding differences)
- ❌ MATERIAL ERRORS — list fields that need re-extraction

If corrections are needed, output a corrected JSON block I can copy directly into the app.

---

**GEMINI EXTRACTION (scratchpad + JSON):**

[Paste Gemini's complete response here]
````

---

## Tips for Effective Audits

- **One company per conversation** — makes it easy to search later ("AVGO FY2025 Audit")
- **Upload the same filing** — Claude needs the source document to verify against
- **Paste the full Gemini response** — the scratchpad gives Claude context about where Gemini found each number, making the audit faster and more thorough
- **If Claude finds errors** — it will produce a corrected JSON you can paste directly into the app. No need to re-run Gemini.
