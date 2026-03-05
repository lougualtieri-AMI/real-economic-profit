# Claude Quarterly Audit Prompt — Real Economic Profit Analyzer
## Version 1.3 | March 5, 2026
## Companion to: 10-Q TTM Extraction Guide v1.2

---

## How to Use This Prompt

This is the **single reference document** for the entire REPA quarterly update cycle.

### Quarterly Workflow

| Step | Tool | What You Do |
|------|------|-------------|
| **1. Extract** | NotebookLM | Upload 10-Q + 10-K. Paste the TTM Extraction Prompt (v1.2). Get back scratchpad + JSON. |
| **2. Audit** | Claude | Paste this entire prompt into a new Claude chat. Then paste the NotebookLM output + prior period JSON. Claude runs Parts 1–4 and auto-generates a company-specific Gemini research prompt. |
| **3. Research** | Gemini Deep Research | Copy the Gemini prompt Claude generated in Part 4 into Gemini. Get back a factual research report. |
| **4. Brief** | Claude | Paste the Gemini report back into the same Claude chat. Claude generates the Quarterly Brief PDF (Stage 2). |

**One prompt. Four steps. Three tools. One PDF at the end.**

---

## The Prompt

Copy everything inside the fence below and paste it into Claude along with your inputs:

````
You are a senior financial analyst auditing a TTM (Trailing Twelve-Month) data extraction for the Real Economic Profit Analyzer.

INPUTS PROVIDED:
1. THE NOTEBOOKLM TTM EXTRACTION — scratchpad (showing TTM build math) + JSON data block
2. THE PRIOR PERIOD REPA DATA — most recent 10-K JSON or prior TTM JSON (for delta comparison)
3. OPTIONAL: Earnings call transcript, news summary, or other context

GLOBAL RULES:
- Use "Full Cost view" and "Excluding Amortization view"
- Excluding Amortization caveat: "amortization of acquired intangibles may not be a real cash cost"
- Use "N/A" (not "N/M") for metrics that cannot be meaningfully calculated
- All values in MILLIONS ($M)
- When net debt is material, explain its impact on Enterprise Value and ownership yield in plain language (e.g., "Net debt of $24B increases Enterprise Value to $340B — 1.3× the $262B Market Cap — reducing the ownership yield from 4.1% to 3.2%"). Do not use D/E ratio as a standalone metric.

WORKFLOW — TWO STAGES:
- STAGE 1: Complete Parts 1–4 as formatted text in chat. Do NOT generate PDFs.
- STAGE 2: When the user provides the Gemini research report and share price, generate a single Quarterly Brief PDF.

══════════════════════════════════════════
STAGE 1 — AUDIT & GEMINI PROMPT
══════════════════════════════════════════

PART 1 — TTM DATA VERIFICATION
Check the TTM build math in the scratchpad:
- Verify the formula: TTM = Current YTD + Prior Year Annual − Prior Year Same-Quarter YTD
- Confirm the right comparative periods were used
- Spot-check at least 5 flow items by redoing the TTM math from the scratchpad numbers
- Confirm balance sheet items use the most recent quarter-end (not summed)
- Verify cash and short-term investments are separated (not combined)
- Check unit conversions (thousands → millions)
- Flag any items where the extractor used estimates (depreciation ratio, tax from annual, etc.)
- Verify inventory/receivables changes use same-quarter YoY (not vs. 10-K year-end)

Verdict: ✅ Clean, ⚠️ Minor issues (list them), ❌ Redo needed

If corrections needed: output corrected JSON block.

PART 2 — DELTA ANALYSIS
Compare TTM results against the prior period provided. For each metric below, show:

| Metric | Prior Period | TTM | Δ | % Δ | Signal |

Metrics:
- Revenue
- Cash From Operations
- CFO Margin (CFO / Revenue)
- Real SBC (MAX of expense vs grant value)
- SBC % of CFO
- Total CapEx
- D&A
- Real Economic Profit (Full Cost view: CFO − Real SBC − Maint CapEx − Restructuring)
- Real Economic Profit (Excluding Amortization view: same but add back amortization of acquired intangibles)
- Real ROIC (Real Profit ÷ Invested Capital, where Invested Capital = Equity + Debt − Cash − STI − Non-Current Securities)
- Net Income
- Total Debt
- Cash & STI (combined for comparison)
- Net Debt (Total Debt − Cash − STI)
- Diluted Shares Outstanding
- Total Shareholders' Equity
- R&D Expense (if applicable)

Signals:
- 🟢 < 5% change
- 🟡 5–10% change
- 🔴 > 10% change
- For margins/rates: 🟢 < 2pp, 🟡 2–4pp, 🔴 > 4pp

PART 3 — WHAT CHANGED AND DOES IT MATTER?
Plain English for an intelligent non-financial reader:

3.1 — Earnings Power Assessment
- Is the earnings power trajectory improving, stable, or deteriorating vs. the prior period?
- What's driving the biggest deltas? (Revenue growth? CapEx ramp? SBC creep? Working capital?)
- Any red flags from the latest 10-Q that weren't in the 10-K?
- 2-3 most important takeaways from this quarterly check-in
- What to watch before the next quarter

3.2 — Amortization Assessment (CONDITIONAL)
Trigger: Full Cost and Excluding Amortization views produce materially different results (opposite profit signs, ROIC gap > 5pp, or yield gap > 2pp). If views are similar, state briefly: "Full Cost and Excluding Amortization views are consistent — no material divergence" and skip.

When triggered:
- Identify specific acquired intangibles driving amortization (from footnotes): category, dollar amount, remaining useful life
- Assess value retention vs genuine erosion for each major intangible
- Recommend which view is more appropriate for THIS company
- For serial acquirers: is amortization a recurring cost of doing business or a non-cash purchase accounting artifact?

3.3 — SBC Trajectory & Value Leakage
- Industry/stage benchmarking (mature large-cap tech: 10-20%, high-growth SaaS: 25-40%, non-tech industrial: <5%)
- Trend direction with specific percentages (SBC % of CFO this TTM vs prior period)
- Value Leakage flag: if SBC expense growth outpaces CFO growth, flag explicitly with growth rates
- If SBC/CFO > 25% or trending up while CFO flat/declining → flag prominently

3.4 — Three-Tier Yield Interpretation
Compute and present three yield tiers using current share price (ask user if not provided):
- Wall Street Earnings Yield: GAAP Net Income / Market Cap
- Real Earnings Yield: Real Economic Profit / Market Cap
- Real Ownership Yield: Real Economic Profit / Enterprise Value (where EV = Market Cap + Net Debt)

Explain:
- What drives the gap between Wall Street and Real Yield? Quantify each factor (SBC adjustment, maintenance CapEx vs depreciation, amortization treatment)
- What drives the gap between Real and Ownership Yield? State net cash/debt in dollars, explain how it changes Enterprise Value relative to Market Cap
- Is the yield cascade telling a consistent story? If Wall Street attractive but Real Ownership low/negative, explain what's lost in translation
- Delta vs prior period: are yields improving or deteriorating, and why?

3.5 — R&D Growth Potential (CONDITIONAL)
Trigger: R&D Expense / Capital Expenditures >= 3.0x. If below 3.0x, state the ratio briefly and skip.

When triggered:
- State in one sentence what the company's R&D spending funds
- Calculate: R&D Expense ÷ Enterprise Value = R&D Yield Spread (in percentage points)
- State: "Each 10% of R&D the reader considers growth investment adds approximately [spread ÷ 10] percentage points to the Excluding Amortization earnings yield of [X%]."
- Note: GAAP R&D may be inflated by IRC Section 41 tax credit incentives. The reported figure likely includes a mix of genuine research, near-term development, and relabeled operational costs. Do not assume or recommend a percentage — the reader applies their own judgment.

3.6 — Valuation Scenario (CONDENSED)
Two-row scenario table showing what the current price requires vs. a baseline earnings power case:

| Scenario | Required Real Profit | Implied Revenue (at current RP margin) | Key Assumption | Evidence Assessment |
|---|---|---|---|---|
| Current price implies | $X | $Y | [growth rate needed] | Supported / Contradicted / Unproven |
| Earnings power baseline | $X | $Y | [flat real profit] | Supported / Contradicted / Unproven |

Brief bear case: "If [specific risk] materializes and SBC reaches $Y, Real Profit could stagnate at $Z — a permanent W% yield at current market cap."

PART 4 — GEMINI QUARTERLY RESEARCH PROMPT
Generate a ready-to-paste Gemini Deep Research prompt.

Structure the prompt in two clean sections — a compact data header followed by numbered research categories. Do NOT embed metrics inline within category descriptions.

**Section A — Company Context & Key Metrics (header block)**
- Company: [Name] ([Ticker], [Exchange])
- TTM period: [period]
- Research window: [day after TTM end] through today
- Key metrics: Real Economic Profit (Full Cost) | Real Economic Profit (Ex-Amort, if applicable) | CFO | Real ROIC | Revenue | Real Yield | CapEx | R&D | SBC (grant value) | Net Debt/(Cash)
- Key deltas from prior period: [3-4 most significant changes with magnitude and apparent driver]
- Risk flags: [bulleted list, one line each]

**Section B — Research Categories**
Six numbered categories (add Category 7 only if R&D/CapEx >= 3.0x):

(1) QUARTER DEVELOPMENTS — earnings call highlights; management commentary on demand outlook, revenue guidance, and company-specific operational topics apparent from the audit; exact guidance figures

(2) PROFIT MOMENTUM EVIDENCE — demand trends in key revenue segments; name specific customers, products, and end markets; pricing trends, volume metrics, backlog, bookings, wins/losses with dollar amounts

(3) RISK UPDATES — for each risk flag listed above: status (worsening / stable / improving) + supporting evidence; plus any NEW material risks not previously flagged

(4) FORWARD GUIDANCE & CONSENSUS — next-quarter and full-year revenue/EPS guidance; analyst consensus and any revisions since most recent earnings; price target range

(5) NEAR-TERM CATALYSTS — specific events in next 90 days that could materially affect revenue or Real Economic Profit; name relevant conferences, customer earnings dates, regulatory deadlines, construction milestones, contract renewals

(6) INDUSTRY/MACRO CONTEXT — supply chain conditions, end-market demand trends, competitive dynamics, regulatory environment as applicable to this company's REP trajectory

(7) R&D GROWTH PERCENTAGE [only if R&D/CapEx >= 3.0x] — Based on this company's disclosed R&D activities, pipeline stage mix, and industry norms, what percentage of total R&D spending would reasonably be classified as growth investment vs. maintenance of the existing business? Cite comparable company disclosures, analyst estimates, or academic research on R&D capitalization rates in this industry.

Close with: "Do NOT provide investment recommendations or subjective assessments. Report verified facts with sources. If a claim cannot be sourced, label it unverified. Organize findings under the numbered categories above."

Output the complete prompt inside a code block for one-click copying.

██ STOP. Output Parts 1–4 as formatted text in chat. Wait for the Gemini research report. Ask for current share price if not already provided. ██

══════════════════════════════════════════
STAGE 2 — QUARTERLY BRIEF PDF
══════════════════════════════════════════

When the user provides the Gemini research report, ask for a shareable link (Google Docs or URL) to include in the report. Then generate a single PDF.

──────────────────────────────────────────
PRE-PDF CHECKLIST
──────────────────────────────────────────
Before generating the PDF, verify these figures are identical across all headers, tables, body text, and the Gemini prompt in Part 4:
□ Real Economic Profit (Full Cost and Ex-Amort if applicable)
□ Real ROIC
□ Real Yield and Wall Street Yield
□ CFO, SBC, Depreciation, Invested Capital
□ Share count and market cap
□ Net Debt / Net Cash
Source of truth: Part 2 delta analysis. Resolve ALL discrepancies before proceeding.

──────────────────────────────────────────
QUARTERLY BRIEF: [TICKER] — [TTM Period]
──────────────────────────────────────────
Audience: Smart non-financial reader. Standalone quarterly update someone could share.
Subtitle: "[Company] | TTM ending [date] | Share price: $X as of [today]"
If research link provided: display prominently below subtitle.

Header banner: 4 colored metric boxes
Real Profit | Real ROIC | Real Yield | TTM Period

Sections:

Q1: The Quarter at a Glance
- 3-4 sentence summary: what this company earned on a TTM basis, how it compares to the prior period, and whether the trajectory is improving or deteriorating.
- Key findings from the Gemini research: earnings call highlights, beat/miss on guidance, management quotes on visibility or outlook. Include specific numbers (revenue beats, EPS beats, guidance ranges).
- Delta highlight box: the 3 most significant changes from Part 2 with arrows and context.

Q2: Where the Money Goes (TTM Waterfall)
- Full Cost view: CFO → minus Real SBC → minus Maintenance CapEx → minus Restructuring = Real Economic Profit
- If views diverge materially: show Excluding Amortization view alongside with brief note on which is more informative (reference Part 3.2)
- Show the prior period waterfall alongside for comparison
- Brief narrative on what drove the change

Q3: What Actually Changed
- Full delta table from Part 2 with signal indicators
- Revenue breakdown by segment with Gemini-sourced data (quarterly performance, YoY/QoQ growth, drivers)
- Demand evidence from customer ecosystem: bullets citing specific customer earnings, order volumes, product announcements, and industry data from Gemini research — with dollar amounts
- Walk through each material delta in plain language: what happened, why it matters for real earnings power, whether it's likely temporary or structural
- SBC trajectory and Value Leakage assessment from Part 3.3
- Working capital trends if material

Q4: The Yield Picture
- Three-tier yield table: Wall Street Yield → Real Yield → Real Ownership Yield (from Part 3.4)
- Prior period comparison showing direction
- Brief narrative: what explains the gaps, and is the valuation story improving or deteriorating
- If net debt is material: plain-language explanation of debt's impact on ownership yield
- If R&D/CapEx >= 3.0x: include R&D yield spread and per-10% add-back statement from Part 3.5

Q5: Post-Quarter Reality Check
- Integrate the Gemini research findings
- Bucket into three groups:
  - **Confirmed positive:** Facts that support improving earnings power trajectory
  - **Confirmed concerning:** Facts that represent real risks to earnings power
  - **Genuinely uncertain:** Material questions where available evidence doesn't clearly point one way
- For each bullet: include specific quantitative data points and management quotes from the Gemini research
- Analyst price target table if available (firm, analyst, target, date)
- Management guidance vs. what the numbers show
- Valuation scenario from Part 3.6 with brief narrative

Q6: What to Watch
- 3-4 concrete monitoring items for next 90 days in table format
- Each: what + when (specific dates), why it matters for Real Economic Profit
- Industry/macro context snapshot: 3-4 bullets from Gemini research

End with: "Note: This analysis depreciates all PP&E as a cost. For companies with potentially appreciating assets (real estate, data centers, infrastructure, land), actual economic costs may be lower than modeled, and Real Economic Profit may be understated. This may warrant further analysis."

Footer: generation date | TTM period | share price + date | research date

DESIGN SPECS:
Colors: Green #26ae60 | Blue-gray #2b3d4f | Amber #f29c11 | Navy #1a2331 | Alt row #edf2f6 | Gray #7e8c8c
Typography: Helvetica. Title 22pt bold, headings 16pt bold, subheadings 12pt bold, body 9.5pt, tables 8.5-9pt.
Layout: Letter size, ~0.6" margins. Use colored header banner boxes for top-line metrics. Tables with navy headers and alternating row shading. Bullet points for Gemini research findings throughout.
````

---

## Tips for Best Results

### For Claude (Steps 2 & 4)
- **Always provide the prior period JSON** alongside the NotebookLM output. Without it, Claude can't compute deltas.
- **Provide the current share price** in Step 2 so Claude can compute yields in Part 3.4. If you forget, Claude will ask.
- **If Claude's TTM math disagrees with NotebookLM's**, trust Claude's corrections — NotebookLM occasionally misreads comparative columns.
- **You can add optional context** (earnings call transcript, news articles) alongside the NotebookLM output in Step 2. Claude will incorporate it into Parts 3 and 4.

### For Gemini (Step 3)
- **Don't edit the prompt Claude generates** unless you want to add specific research questions.
- **Gemini Deep Research works best** when the prompt is specific — that's why Claude names actual customers, dollar amounts, and risk factors.
- **The research window matters.** Claude sets it to start the day after the TTM period ends, preventing Gemini from rehashing information already in the filing.

### Quarterly Cadence
| When | What to Do |
|------|-----------|
| After Q1 10-Q filed | NotebookLM (Q1 10-Q + prior 10-K) → Claude audit → Gemini → Claude PDF |
| After Q2 10-Q filed | NotebookLM (Q2 10-Q + prior 10-K) → Claude audit → Gemini → Claude PDF |
| After Q3 10-Q filed | NotebookLM (Q3 10-Q + prior 10-K) → Claude audit → Gemini → Claude PDF |
| After 10-K filed | Full annual extraction (10-K extraction prompt) → Full annual audit (v4.2) → Fresh baseline |

---

## What This Prompt Covers vs. The Annual Audit (v4.2)

| Feature | Annual Audit (v4.2) | Quarterly Audit (v1.3) |
|---------|---------------------|------------------------|
| Data verification | Field-by-field (27 fields) | TTM math verification |
| Delta analysis | Multi-year trend | TTM vs prior period |
| Amortization assessment | Full (Part 3A) | Condensed, conditional (Part 3.2) |
| SBC commentary | Full (Part 3B) | Condensed (Part 3.3) |
| Yield interpretation | Full 3-tier (Part 3C) | Full 3-tier (Part 3.4) |
| R&D growth potential | Full (Part 3D) | Condensed, conditional (Part 3.5) |
| Valuation scenario | Full scenario table (Part 6 / S4) | Condensed 2-row (Part 3.6) |
| Risk flags | Full (Part 4) | Focused on new risks (Part 3.1) |
| Gemini research prompt | Full (Part 5B, 6-7 categories) | Quarterly-focused (Part 4, 6-7 categories) |
| Research quality review | Full (Part 6B) | Not included — lighter cadence |
| Investment synthesis | Full scenario analysis (Part 6) | Post-quarter reality check (Q5) |
| Output | 3 PDFs (Snapshot, Audit, Research) | 1 PDF (Quarterly Brief) |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | Feb 24, 2026 | Initial release — two-stage audit + PDF |
| 1.1 | Feb 24, 2026 | Consolidated into single document. Added Gemini prompt template. Enhanced Stage 2 PDF spec. Added workflow summary, tips, and cadence table. |
| 1.2 | Feb 25, 2026 | Added Part 3.2 (Amortization Assessment). Added Part 3.3 (SBC Trajectory). Added Part 3.4 (Three-Tier Yield). Added Real Yield to header metrics. Added both views to delta table. Added Q4 Yield Picture. Added Pre-PDF Checklist. Aligned Global Rules with annual prompt. |
| 1.3 | Mar 5, 2026 | Added Part 3.5 (R&D Growth Potential, conditional, mirrors annual Part 3D). Added Part 3.6 (Condensed Valuation Scenario). Added Category 7 to Gemini prompt (conditional on R&D/CapEx ≥ 3.0x). Restructured Gemini prompt into header block + category list to reduce Gemini context failures. Added R&D yield spread to Q4 Yield Picture in PDF. Added scenario output to Q5 in PDF. Updated comparison table. |
