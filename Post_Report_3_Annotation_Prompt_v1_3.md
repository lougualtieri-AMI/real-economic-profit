# ANNOTATED AUDIT COMPANION — POST REPORT 3 — v1.3

## Purpose
After a full REPA audit cycle is complete (Gemini Deep Research report + REPA
Report 3 generated + REPA Brief available), produce a single annotated
companion document that reproduces the Gemini report verbatim with inline
Claude Comments layered in. This is the richest annotated treatment in the
REPA suite — it goes deeper than the brief annotation comments embedded in
Report 3, and it is designed to stand alone as a comprehensive read without
requiring the reader to also have Report 3 open.

This workflow is category "04 Annotated" in the REPA Knowledge Center folder
structure — distinct from 01 Snapshot, 02 Full Audit, and 03 Research &
Outlook.

Two outputs per run:
1. A PDF with the full Gemini text and inline Claude Comments (blue-accent
   styled blocks)
2. A companion Markdown file optimized for text-to-speech (Speechify) and
   linear reading

## When to Run This — Occasional, Not Routine

This is NOT a default deliverable in every audit cycle. It is triggered by
material content, not by schedule.

The Claude Audit Prompt (v1.0) includes a closing nudge
that surfaces the decision at the end of each cycle. Run the Annotated
companion when ANY of these are true:

- The cycle surfaced a material new development (M&A, restructuring,
  guidance reset, regulatory action, leadership change, accounting change)
- It is the first audit cycle on a new portfolio entrant
- It is the annual 10-K cycle (not a routine quarterly TTM update) AND the
  filing carried meaningful new disclosure
- The company is in the speculative sleeve (SDGR, pharma names) where
  information density per filing is high
- The user explicitly requests it for a record-keeping reason

Skip it (or defer to the next cycle) on routine quarterly TTM updates of
stable core names where the Gemini report is largely confirmatory and Report
3 already captures the relevant items inline.

If the cycle nudge or the user is uncertain whether to run it, default to
SKIP. Producing a thin annotated report on a quiet quarter is worse than
producing none.

## Inputs Required
- Gemini Deep Research report (PDF or MD, usually already in context from the
  audit cycle)
- REPA Report 3 (PDF) — used as reference context, NOT reproduced
- REPA Real Economic Profit Brief (MD) — used as reference context, NOT
  reproduced
- Optionally: a Google Docs link to the Gemini report for hyperlinking on the
  title page

## How This Differs From the Other Annotation Workflows
- The **General Business Annotation** (v1.3 Project Instructions) runs on
  Gemini reports that were never built with REPA in mind — its job is to
  introduce REPA framing to a non-REPA document. It runs automatically when a
  Gemini report is uploaded to that project.
- The **Post-Ceiling and Post-Floor annotations** (their respective Project
  Instructions) run on specialized Gemini analyses of existential risk and
  capital preservation.
- **THIS workflow** runs on the standard Gemini earnings/quarterly research
  report AFTER a full REPA audit cycle is complete (Report 3 + Brief in hand).
  Because REPA metrics already exist for this company at this date, comments
  can anchor directly to the three-tier yield cascade, the TTM waterfall, SBC
  Value Gap, and DR/CFO ratio — with exact figures, not framework
  introductions. This is the deepest annotated treatment in the suite.

## Execution: Two Phases

### Phase 1 — Research & Comment Drafting
Produce a structured text deliverable containing everything needed for
rendering. Do NOT generate the PDF or Markdown yet.

Phase 1 output must include:

1. **Report metadata:** Ticker, company name, Gemini report date, annotation
   date, share price (intraday today + end-of-period anchor), REPA Report 3
   reference (TTM period + generation date).
2. **Research log:** A brief list of any web searches performed to fill gaps
   the Gemini report + Report 3 + Brief could not cover, with one-line
   summaries of what each surfaced.
3. **Claude Comments:** As many as the material findings warrant — see the
   Quality Gate below. Each as a structured block:
   - **Section anchor** — exact Gemini heading or opening phrase the comment
     will follow
   - **Comment title** — short descriptive label
   - **Comment body** — full paragraph with specific numbers, dates, ratios,
     filing references. Free to be REPA-anchored OR to cover anything material
     (competitive, regulatory, insider activity, SBC trajectory, legal
     overhangs, macro).
   - **Sources** — inline source notes (SEC filing + date, transcript, URL
     where relevant)
4. **Risk Flag Summary drafted in full:** organized by materiality tier
   (High / Medium / Low-Watch), each flag a short paragraph.
5. **Catalyst Timeline drafted in full:** table format with Date, Catalyst,
   Significance.
6. **Consolidated sources line** for the final page.

End Phase 1 by pausing for confirmation. Example:
"Phase 1 complete. Review the comments, Risk Flag Summary, and Catalyst
Timeline above. Reply 'proceed' to render the PDF and Markdown, or request
specific edits first."

### Phase 2 — Rendering
Once the user confirms, produce both deliverables using Phase 1 content
verbatim. Phase 2 is pure rendering — no re-research, no rewrites unless the
user requests a specific edit.

## Quality Gate — Comment Inclusion Rule

There is no count target. Comments are included when they pass BOTH tests:

1. **Concrete content test:** The comment must contain at least one specific
   number, date, ratio, or filing reference. A comment without a verifiable
   anchor fails this test.
2. **Additive content test:** The comment must convey information not already
   present in the Gemini text or REPA Report 3. A comment that restates,
   summarizes, or rephrases existing content fails this test.

A comment that fails either test gets cut. The final count is whatever
survives — which may be three on a quiet 10-Q cycle or eighteen on a complex
10-K with M&A activity. Do not pad to hit a number; do not cap to stay
within one. The cycle nudge in the Claude Audit Prompt (v1.0) should already have filtered out
quiet quarters before this prompt runs, so when it does run, expect the
count to land in the high single digits or low teens — but treat that as
descriptive, not prescriptive.

## Comment Topic Coverage

When evaluating where to find comments, ensure coverage of these topics
where material to the company. Not every topic needs a comment; pick the
ones with the largest gaps between what Gemini says and what the full REPA
+ public-filing picture shows:

1. **Yield cascade compression vs. price action** — REPA's three-tier yields
   at current price vs. end-of-period anchor vs. prior fiscal year close. Is
   yield compression coming from REP growth or from multiple expansion?
   Quantify.
2. **SBC Value Gap and trajectory** — grant-date fair value vs. expense, MAX
   rule treatment, unrecognized compensation pipeline, implications for
   forward REP drag.
3. **Deferred revenue / working capital quality** — if DR is a material CFO
   driver, quantify the ex-DR REP. Compare management's own FCF guide to the
   DR trajectory.
4. **Maintenance CapEx methodology / Full Cost vs. Ex-Amortization** —
   intangible amortization decomposition, yield gap between the two REPA
   views.
5. **Customer / revenue concentration** — what Gemini omits about top-customer
   or segment-level concentration, 10-K disclosure thresholds.
6. **Competitive shifts** — M&A in the competitive set, scale comparisons,
   architectural positioning that Gemini understates or misses entirely.
7. **Insider activity** — Form 4 filings, selling/buying patterns in the
   quarter, 10b5-1 plan status, dollar totals.
8. **Guidance internals** — disaggregate management's guide. Did revenue/EPS
   move without FCF moving? What does that signal?
9. **Rating agency / capital structure sequencing** — timing and basis of IG
   upgrades, refinancing terms, leverage covenants vs. current leverage.
10. **Legal / regulatory overhangs** — pending litigation, enforcement
    matters, settlement or dismissal status.
11. **Macro / tariff / grid bottleneck mechanics** — not the existence of the
    headwind (Gemini covers that), but the SPECIFIC MECHANISM by which it
    affects the company's economics (e.g., revenue recognition timing, BOM
    exposure, deferred revenue dynamics).
12. **Something Gemini entirely skips** — every Gemini report has a blind
    spot. Find it. For pharma it's often pipeline cadence or patent cliff
    math; for industrials it's often SBC trajectory or customer concentration;
    for financials it's often loan book detail.

## Formatting Specs

### PDF Layout
- Full-width layout, US Letter, 0.9" margins
- Main body: 11pt Helvetica, justified
- Claude Comment blocks: 10pt italic, light-blue (#EEF3FA) background, 3pt
  blue (#1E5AA8) left border, "Claude Comment — {Title}" label in bold blue
- Compact running header on body pages:
  "{TICKER} Gemini Report Annotation | Page X"
- Title page: ticker + company name, "Gemini Deep Research — Annotated"
  subtitle, Gemini report date, annotation date, share price, end-of-period
  anchor, REPA Report 3 reference, Google Docs hyperlink if provided, and the
  standard disclaimer at the bottom of the title page ONLY (not repeated
  anywhere else).
- End sections: Risk Flag Summary (prose paragraphs under H2 tier headings),
  Catalyst Timeline (table: Date / Catalyst / Significance), Consolidated
  Sources line.

### Title Page Disclaimer (once, title page only)
"Claude Comments contain independently researched data from SEC filings and
public sources. They are factual annotations, not investment advice. REPA
metrics reference the Full Audit: {fiscal year range}, share price
{share price}, audit date {audit date}. This analysis depreciates all PP&E as
a cost; for companies with potentially appreciating assets, actual economic
costs may be lower than modeled."

### Markdown Companion (TTS-optimized)
- Preserve Gemini section headings as MD headings (##, ###)
- Full Gemini text verbatim under each heading
- Claude Comments as blockquotes with bold labels:
  "> **Claude Comment — {Title}**"
- Write for TTS: spell out numbers ("2 billion, 73 million dollars" not
  "$2,073M"), "percent" not "%", "year-over-year" not "YoY", expand
  abbreviations on first use ("stock-based compensation" before "SBC",
  "Europe, Middle East, and Africa" before "EMEA")
- Include disclaimer once at top, not repeated
- Same Risk Flag Summary and Catalyst Timeline at end, in prose form

### Filename Conventions

**PDF (category 04 Annotated):**
`{TICKER} 04 Annotated AUDIT POST REPORT 3 {YYYY-MM-DD}.pdf`

**Markdown companion:**
`{TICKER}_04_Annotated_Gemini_Report_{YYYY-MM-DD}.md`

Where `{YYYY-MM-DD}` is the annotation date (the date this document is
generated), not the Gemini report date. The "04" prefix aligns with the REPA
Organizer category system (01 Snapshot / 02 Full Audit / 03 Research &
Outlook / 04 Annotated).

## What a Good Claude Comment IS
- A completed paragraph containing actual researched data — specific numbers,
  dates, ratios, filing references
- Independently sourced or drawn from Report 3 / the Brief / the 10-K / 10-Q
- A factual enrichment, correction, or contextualization of the adjacent
  Gemini text
- Self-contained: every comment delivers a finding, not a suggestion to
  investigate
- Free to exceed the scope of REPA where relevant (competitive, insider,
  legal, operational) — REPA-anchored, not REPA-constrained

## What a Good Claude Comment is NOT
- A generic observation without data ("SBC is worth watching")
- A restatement of what Gemini already said
- A suggestion ("investors should check…", "it's worth examining…")
- A disclaimer or caveat about limitations
- A reference that requires the reader to also have Report 3 open

## Execution Notes
- Always read the PDF skill (`/mnt/skills/public/pdf/SKILL.md`) before
  generating the PDF
- Use reportlab for PDF generation
- Quality gate is binary: a comment passes both tests or it gets cut. No
  count target overrides this.
- This annotated report is a companion to the full REPA audit suite, not a
  replacement
- Two-phase execution (research/draft → render) is the default; override only
  on explicit one-shot request from the user

---
*Post_Report_3_Annotation_Prompt_v1_3 — May 26, 2026*
*Changes from v1.2: cross-references updated to the Claude Audit Prompt (v1.0); the
separate v4.4 / v1.6 cycle nudges are now one moded nudge inside that master.*
*Changes from v1.1: removed 10–15 count target; replaced with binary quality
gate (concrete + additive). Added "When to Run This" section establishing
occasional-use framing.*
