# Real Economic Profit Analyzer — Glossary & Definitions

Use this document as a reference source in NotebookLM alongside Export Briefs and Gemini extraction work.

> **Reading note.** "Yield" in this framework always refers to a price question — what an owner collects relative to what they pay (`y`, REP ÷ EV, and its cousins). It is never used for a total compounding return. The total-return figure is `k`, the **Future Total Return**, and it is always labeled a total return, never a yield.

---

## 1. Core REP Metrics (Custom to This Framework)

**Real Economic Profit (REP)**
What a company actually earns in cash after accounting for true stock dilution, real asset maintenance, and one-time charges. Formula: CFO − Real SBC − Maintenance CapEx − Restructuring − One-Time Adjustments + Investment Income. This is the central metric of the framework. Every yield, ROIC, ROIIC, and return figure in the app is built on a REP basis.

**Real Ownership Yield**
Real Economic Profit ÷ Enterprise Value. The headline owner's yield: what you would earn buying the whole company, taking on its debt and keeping its cash. A yield of 5% means the business generates $5 of real profit for every $100 of enterprise value. Higher is better; below ~2% is expensive. (Earlier drafts called this "Real Cash Yield" — same number; Real Ownership Yield is the canonical name and the third rung of the Three-Tier Yield below.)

**Three-Tier Yield**
Three earnings yields shown in descending order, each stripping away another layer of distortion:
1. **Wall Street Earnings Yield** — GAAP Net Income ÷ Market Cap. What reported earnings look like to most investors.
2. **Real Earnings Yield** — REP per share ÷ Price. The same price, but earnings cleaned up — real SBC, true maintenance CapEx, and noise removed.
3. **Real Ownership Yield** — REP ÷ Enterprise Value. What you would earn buying the whole company, debt and all.

For cash-rich companies, EV is smaller than Market Cap, so the Ownership Yield runs higher than the Earnings Yield.

**Real ROIC (Return on Invested Capital)**
Real Economic Profit ÷ Invested Capital. Measures how efficiently the company uses its deployed capital. Above ~12% is strong; below ~6% is concerning. The base figure deducts full D&A × 1.05 as maintenance CapEx; the Best Guess and FTR views compute a *symmetric* Real ROIC that capitalizes growth R&D into the base (see §3 and §4).

**Return on NTA (Net Tangible Assets)**
Real Economic Profit ÷ Net Tangible Assets (Total Assets − Goodwill − Intangibles − Cash − STI). Strips out acquisition premiums and buyback-compressed equity to show what the operating assets actually earn. The better read for serial acquirers, whose goodwill sits in the ROIC denominator and can understate the true operating return.

**Real P/E**
Share Price ÷ Real Economic Profit per share. An alternative to traditional P/E using real profit instead of reported earnings. The inverse of the Real Earnings Yield.

**Unlevered Yield**
(Real Economic Profit + Interest Paid) ÷ Enterprise Value. The yield as if the company had no debt — useful for comparing companies with different capital structures.

**Real vs. Reported Gap**
Real Economic Profit − Reported Free Cash Flow (CFO − Total CapEx). A negative gap means reported FCF overstates real earnings, typically because SBC dilution is not deducted in traditional FCF.

---

## 2. Real SBC (Stock-Based Compensation)

**Real SBC Burden**
The greater of SBC Expense (from the cash flow statement) or SBC Grant-Date Fair Value (units granted × weighted-average grant price). The MAX function captures the true dilution cost to shareholders, since the accounting expense often understates the economic value being transferred to employees.

**Hidden SBC Dilution**
Flagged when SBC Grant Value exceeds SBC Expense by more than 20% — the accounting expense understates the value being transferred, often because grants were made at higher stock prices than the older grants now being amortized.

---

## 3. Maintenance CapEx & the Two Cost Views

Real ROIC and REP are computed under two reference views that differ only in how they treat acquired-intangible amortization.

**Maintenance CapEx — Full D&A**
Full Depreciation & Amortization × 1.05 (when not company-disclosed). The conservative floor: treats all depreciation *and* amortization as real annual replacement cost. The 1.05 multiplier is an inflation buffer — replacing aging assets costs more today than their historical cost. Penalizes acquisitive companies, which may be appropriate.

**Maintenance CapEx — All Amortization Added Back**
Physical Depreciation Only × 1.05, excluding intangible amortization. For acquisition-heavy companies, most D&A is amortization of purchased intangibles (customer lists, technology, trade names) that do not require equivalent cash to maintain — the assets often retain or grow in value even as they amortize to zero on the books. Available only when depreciation is broken out separately from amortization.

*(Naming note: these two views were formerly labeled "Full Cost" and "Excluding Amortization." Those labels are retired.)*

**Depreciation Intensity**
Depreciation ÷ Net Tangible Assets. A proxy for capital intensity. Above 15% = high intensity (the Full D&A view is more reliable); 8–15% = mixed; below 8% = low intensity. Lease-heavy companies can show elevated ratios from right-of-use asset depreciation.

---

## 4. The Yield Ladder & Best Guess

The Yield Ladder shows three side-by-side REP yields, each on a different maintenance-cost basis. The first two are fixed reference points; the third is the analyst's tunable central case.

**① Full D&A** — REP with full D&A × 1.05 as maintenance CapEx. The conservative floor (see §3).

**② 100% Amort / 0% R&D Added Back** — REP with all intangible amortization added back to the numerator and no growth-R&D treatment. The acquisition-amortization ceiling.

**③ Best Guess ★** — the analyst's central case, sitting between the two reference points. Built as Full D&A REP plus a slider-set fraction of amortization add-back plus a slider-set fraction of after-tax growth R&D. This is the REP basis that feeds the Future Total Return panel.

**Best Guess Controls (sliders)**
Two sliders tune the Best Guess panel:
- **Amortization add-back %** — how much of intangible amortization to treat as accounting fiction rather than real cash cost.
- **R&D add-back %** — the discretionary *growth* share of R&D to credit back into profit. Applied after-tax at (1 − statutory rate), because expensing R&D generates an immediate tax shield that capitalizing would forfeit. The remainder is treated as ordinary maintenance R&D (bug fixes, security patches, patent replacement, regulatory compliance).

**Symmetric R&D Capitalization**
A consistency rule for the Best Guess views: when growth R&D is credited back into REP (the numerator), it must also be capitalized into the capital base (the denominator), so ROIC is not overstated. Growth R&D is capitalized straight-line over a sector-based asset life — **Software/Platform 3 years, Semiconductor 5 years, Pharma 7 years** — pre-tax, and added to Invested Capital. The amortization add-back rides the **numerator only**. "Symmetric" because both sides of the ratio move together. (Symmetric is the canonical method, per the C3 determination and GC Decision 2; the older "asymmetric" framing — R&D in the numerator only — is superseded.) Full D&A leaves R&D out of both sides.

**Best-Guess ROIC**
Best Guess REP ÷ (Invested Capital + capitalized growth R&D) — the symmetric, R&D-capitalized ROIC, shown on the Best Guess rung of the ladder and the same figure the FTR panel uses as its `r` seed. Color bands: ≥12% strong, ≥6% adequate, <6% concerning. Shown as "n/a — negative equity" when Invested Capital is ≤ 0. Because the goodwill-heavy base absorbs the capitalized R&D, an acquisitive name can read materially higher here than on the Full D&A basis.

---

## 5. Capital Allocation Analysis

The panel that asks how well the company turns retained capital into incremental profit.

**Invested Capital (IC)**
Shareholders' Equity + Total Debt − Cash − Short-Term Investments. The total capital deployed in the business from both equity and debt holders, net of liquid assets.

**Net Tangible Assets (NTA)**
Total Assets − Goodwill − Intangibles − Cash − STI. The physical and working-capital assets actually deployed in operations, excluding acquisition-related paper assets and liquid holdings.

**Rolling ROIIC (Return on Incremental Invested Capital)**
ΔREP ÷ ΔIC over a trailing window — what each *new* dollar of capital has earned. Single-period ROIIC is too noisy to use raw, because ΔIC is a small difference of large numbers and routinely swings or goes negative. The app shows **1-, 3-, and 5-year** windows; the **3-year window is the canonical signal**, feeding the FTR sensitivity band and the "recent trend" line. (ΔIC uses the same R&D-capitalized base as Best Guess ROIC; windows where ΔIC is non-positive or trivially small are flagged "not meaningful.")

**R&D as a Capital Investment**
A diagnostic that recomputes ROIC with growth R&D capitalized into the base. Its **Symmetric column** uses the same R&D-capitalized base as Best Guess ROIC but excludes the amortization add-back from the numerator — a deliberately smaller variant for side-by-side comparison.

**Acquirer Flag**
Raised when goodwill exceeds 20% of (R&D-capitalized) Invested Capital. Signals that ROIC may understate the operating return and Return on NTA is often the better anchor.

**Buyback Vintage Analysis**
- **Captured Yield** — (Ownership % × Current REP) ÷ Buyback Cost. The return the company is earning *today* on a given year's buyback spending. Higher = better allocation; compare against T-bill rates to judge whether buybacks beat the risk-free alternative.
- **Buyback Vintage Yield Matrix** — each buyback year's captured yield re-evaluated at every later year's REP, showing how the return on a vintage evolves as REP grows or shrinks.

---

## 6. Future Total Return (FTR)

Your expected annual **total return** from owning the stock — the yield you collect at today's earnings yield, plus the profit growth the company compounds by reinvesting — *if the multiple holds*. The whole figure rests on those assumptions holding, which is the real bet.

**k — Future Total Return** (the panel headline)
Three algebraically equivalent forms:
- Standard: `k = (1 − b)·y + b·r`
- Decomposed: `k = y + b·(r − y)` — the more intuitive reading
- Cash-hoarder: `k = (1 − b − c)·y + b·r + c·r_cash`

`k` is always a total return, never a yield.

**y — current real REP yield**
REP ÷ Enterprise Value. The floor: what an owner collects in year 1 if no growth happens. Observed from the data, not estimated. (Same number as the Real Ownership Yield.)

**r — return on profit reinvested**
What the analyst expects each reinvested dollar to earn going forward — the *operating* return on capital put to work inside the business. An analyst-set input, because the right number depends on the company.
- **Default seed:** the latest period's **symmetric Real ROIC** (Best Guess REP ÷ R&D-capitalized IC) — the same figure shown on the Best Guess rung of the ladder. This is the canonical seed.
- **One-click anchors beside the seed:** Real ROIC, rolling ROIIC over 1- / 3- / 5-year windows (the marginal return on recent capital; **3-year is the recommended read, ★**), and Return on NTA (operating return with goodwill stripped — the better read for acquirers). Set `r` to any of them and adjust from there.
- Do not confuse `r` with `r_cash`.

**b — operational reinvestment rate**
(Growth CapEx + after-tax Growth R&D) ÷ REP. The fraction of *earnings* the company plows back into productive growth — the combined CapEx-and-R&D definition. It is **not** Growth CapEx ÷ IC (the fraction of the capital *base*); that older formulation was the source of the retired Implied Profit Growth bug. The seed is a profit-weighted average over the active range, so one low-REP year cannot hijack it; a single latest-year ratio is shown separately.

**c — cash retention fraction**
The fraction of REP retained on the balance sheet rather than reinvested or distributed. Default 0; positive when cash is accumulating (e.g., AAPL pre-buyback era, GOOGL pre-AI-ramp, mature pharma).

**r_cash — money-market rate**
The return the company earns on retained cash, ~4–5% in the current environment. User-set with a default. Distinct from `r`: `r` is the return on capital deployed *in the business*; `r_cash` is the return on cash that is *not* deployed. The contrast makes the cost of hoarding visible.

**f — financing cost (over-reinvestment)**
The cost of funding growth beyond what earnings cover. Used when `b` exceeds 1 (the company reinvests more than its entire profit). Seeded as Interest Paid ÷ Debt, floored at `r_cash`. The over-reinvested slice is charged `f`, so the steady-state growth becomes `g = b·r − (b−1)·f`. Reinvesting above earnings can be accretive when `r` exceeds `f`, but it is leverage-dependent and reversible if financing or returns shift.

**g — Implied REP Growth**
`g = b·r` — the organic, steady-state rate at which REP compounds if the company keeps reinvesting `b` of its profit at return `r`. A pure operating rate (the over-reinvestment form is `g = b·r − (b−1)·f`). It can read *below* the historical Profit CAGR; that gap is growth the steady-state identity does not capture — typically acquisitions, where REP arrives faster than retention × return implies.

**(r − y) — the value-creation spread**
Positive when reinvestment beats distribution, zero when neutral, negative when reinvestment destroys value relative to distribution. The core diagnostic the decomposed form exposes.

**b·(r − y) — the growth premium**
The portion of Future Total Return contributed by reinvestment *above* what distribution would have produced. If `b` is large but the spread is small or negative, the growth premium shrinks regardless of how aggressively the company reinvests.

**Multiple-Change (Re-Rating) Lever**
A what-if control: drag the ending multiple to see what a re-rating over the same 10-year window does to total return. Applied as a pure factor on top of `k`: all-in return = (1 + k)(1 + m) − 1, where the multiple is the inverse of the yield (EV ÷ REP = 1 ÷ y).

**Year-by-Year Trajectory**
Projects REP ten years forward under the chosen `b`, `r`, and lag assumptions. Because reinvested capital takes time to produce returns, early-year growth runs slower than `g` and accelerates as investments mature.

**WACC — deliberately not used as a hurdle**
Replaced throughout by `y` (current yield) as the investor's reference rate. WACC is the right hurdle for the *company's* internal capital-allocation decisions; `y` is the right hurdle for the *investor's* buy/hold decision. The FTR framework is investor-facing, so `y` is the hurdle.

---

## 7. Look-Through REP

For companies with significant equity-investment portfolios, standard REP only captures dividends received from investees (which flow through CFO). Look-Through REP adds the after-tax retained earnings of those portfolio companies — profits earned on behalf of shareholders that compound inside investee businesses without appearing in CFO.

**Look-Through REP**
Standard REP + portfolio retained earnings. Methodology: portfolio market value × (earnings yield − dividend yield) × 0.97 tax haircut, added to standard REP. Manually supplied — cannot be extracted from 10-K filings.

**Look-Through Yield** — Look-Through REP ÷ Enterprise Value. The more complete yield for companies whose equity portfolios generate unreported earnings power. Shown in a blue-bordered card below the standard yield ladder when the data is present.

**Yield Pickup** — Look-Through REP − standard REP. The additional earning power from investee retained earnings not captured by CFO-based analysis.

**Company-specific notes:**
- **Berkshire Hathaway** — Look-Through Retained Earnings supplied from the Semper Augustus annual letter (audited GAAP retained earnings, ~$10.7B after-tax at a 3% haircut, lifting Look-Through REP to ~$42.5B and REP Yield by ~1.3 points).
- **Alphabet (GOOGL)** — a *proxy*, not a true look-through: the 8-quarter trailing average of cumulative upward fair-value adjustments on the equity portfolio (~$31B/yr through Q1 2026). Explicitly **not** equivalent to BRK's audited-retained-earnings look-through; shown as a supplemental panel only.

---

## 8. Growth, R&D & Reinvestment Inputs

**Growth CapEx** — Total CapEx − Maintenance CapEx. Spending beyond what is needed to maintain the current asset base; the balance-sheet channel of growth investment.

**R&D Expense** — Total R&D from the income statement. Under US GAAP, R&D is expensed immediately, so it already reduces CFO. It is the income-statement channel of growth investment, invisible to a CapEx-only view.

**R&D Ratios** — quick intensity reads: R&D / Revenue (typical 15–25% semis, 10–20% software, 5–15% diversified tech), R&D / CapEx (above 3x means R&D is the dominant investment channel), and R&D / CFO (above ~50% means most cash generation is plowed into research).

**R&D-Dominant Investment Model** — flagged when R&D exceeds ~3x physical CapEx. Indicates the company invests primarily through the income statement. *In the v4 framework this is handled directly by the Best Guess symmetric capitalization* (growth R&D credited to profit and capitalized into the base), not by steering toward a particular cost view.

**Total Reinvestment (CapEx + R&D)** — the full picture of how much a company reinvests in its future. For tech and pharma, R&D can be many times CapEx; comparing CapEx alone across company types is misleading.

---

## 9. Input Fields & Data Sources

**CFO (Cash From Operations)** — net cash from operating activities; the starting point for REP.
**SBC Expense Addback** — stock-comp added back as non-cash in CFO.
**SBC Grant-Date Fair Value** — units granted × weighted-average grant-date fair value, from the SBC footnote.
**D&A** — combined depreciation of physical assets and amortization of intangibles; the basis for estimating maintenance CapEx when not disclosed.
**Depreciation Only** — physical depreciation of PP&E, excluding intangible amortization. Enables the All Amortization Added Back view.
**Amortization** — D&A − Depreciation. The accounting charge for acquired intangibles; for acquisitive companies it can dwarf physical depreciation, and the assets often retain value even as they amortize to zero.
**Total CapEx** — purchases of PP&E from investing activities; entered as a positive number.
**Restructuring Charges** — layoffs, closures, reorganization; deducted from REP. If recurring every year, effectively an ongoing operating cost.
**Investment Income** — income from investments, added into REP.
**Inventory / Receivables Change** — from CFO; positive frees cash, negative traps it.
**Interest Paid / Income Tax Paid** — cash figures from supplemental disclosures; cash taxes often differ sharply from the income-statement provision.
**STI (Short-Term Investments)** — current marketable securities; treated like cash (subtracted from IC and EV).
**EV (Enterprise Value)** — Market Cap + Total Debt − Cash − STI. The price to acquire the whole business. For cash-rich companies, EV is materially below Market Cap.
**Buyback Cost** — total dollars spent on repurchases in the year; feeds Captured Yield.

---

## 10. Red Flags & Signals

**SBC as % of CFO** — Real SBC ÷ CFO. Above 30% is a warning; above 15% is worth monitoring.
**Value Leakage** — SBC growth rate ÷ CFO growth rate (year over year). Below 20% = good; 20–50% = meaningful; above 50% = majority of growth consumed by rising compensation.
**Off-Balance-Sheet / Risk Flags** — extracted from filing footnotes: SPVs, VIEs, contingent liabilities, material commitments, related-party transactions.

---

## 11. Data Pipeline

**SEC 10-K** — annual report; full-year statements, footnotes, MD&A. Primary source for all cash flow items.
**SEC 10-Q** — quarterly report; used to build a TTM dataset (Current YTD + Prior-Year Annual − Prior-Year Same-Quarter YTD). Balance-sheet items use the most recent quarter-end.
**NotebookLM / Gemini Extraction** — processes the filing and extracts structured JSON via a standardized prompt that first produces an analyst scratchpad (sources, math, reasoning), then the final JSON. NotebookLM is preferred for reliability.
**Claude Audit (Annual / Quarterly)** — the extraction output is cross-checked field by field against the source, formulas verified, and a company-specific Gemini Deep Research prompt generated; PDF reports follow once research is returned.
**Gemini Deep Research** — researches post-filing developments, profit momentum, risk-flag updates, guidance, catalysts, and industry context. For R&D-intensive companies (R&D/CapEx ≥ 3x), it also estimates the growth-vs-maintenance split of R&D.

---

*This glossary is current as of June 3, 2026 (v4.15.0). Definitions reflect the Real Economic Profit Analyzer framework and may differ from standard financial-textbook definitions. Standing anchors: symmetric ROIC is canonical; `k = (1 − b)·y + b·r` is always a total return; "yield" is reserved for the `y` leg; `g = b·r`; sector asset lives Software 3 / Semiconductor 5 / Pharma 7.*
