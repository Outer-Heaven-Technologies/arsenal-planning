---
name: market-analysis
description: Produces an executive-grade unified research dossier — market overview (TAM/SAM/SOM bottom-up) + customer JTBD + industry structure with Porter's all 5 forces + conditional PESTLE + SWOT synthesis + risks + strategic recommendations. Runs deep research via `dispatch-parallel` with Jina MCP / Firecrawl / WebSearch; every claim tier-graded (T1–T4) with explicit confidence ratings. Output format scales (Brief 3-5pp / Standard 5-10pp / Comprehensive 10-15pp body; appendix excluded) and is decided at the end based on intent. Standalone — use for product validation, investor decks, market expansion research, adjacent-market scouting, or any project requiring grounded executive analysis. Triggers: "research the market for X", "do a competitive analysis for Y", "validate this market", "build me a market dossier", "executive research on Z", "do deep market research", "should I enter this space".
---

# Market Analysis

Produce an **executive-grade unified research dossier** that a CEO or key decision-maker could read end-to-end and make a strategic call from. Output: `.arsenal/strategy/MARKET_RESEARCH.md`.

The dossier follows MBA / consulting conventions (Pyramid Principle, SCR exec summary, "So what?" closures per section). Research is always thorough — every claim cited with `[source: URL, tier: T1–T4, confidence: H/M/L]`. The output **format** scales to the audience (Brief / Standard / Comprehensive), but the underlying research methodology is uniform.

This skill is standalone. It pairs naturally with `mvp` (which produces the spec) — but `market-analysis` runs on its own for investor decks, strategic planning, market-entry scouting, adjacent-market research, and any case where you need executive-grade analysis without immediately writing an MVP spec.

## Paths

All arsenal artifacts live under `.arsenal/` at the project root.

| What | Path | Notes |
|---|---|---|
| Strategy archive (denied during build) | `.arsenal/strategy/` | MARKET_RESEARCH.md, RESEARCH_PLAN.md, MVP_SPEC.md, mockup-briefs/, GTM_STRATEGY.md, REVENUE_MODEL.md |
| Feature specs | `.arsenal/FEATURES.md` (single-mode) or `.arsenal/features/<slug>.md` (split-mode) | Gated per phase via `.claude/settings.json` |
| Project anchor docs | `.arsenal/{ARCHITECTURE,CONVENTIONS,TASKS}.md` | Always readable during build |
| Design reference set | `.arsenal/design/{UX,DESIGN,DESIGN_SYSTEM}.md` + `.arsenal/design/mockups/` | Always readable during build |
| Per-task briefs + ephemera | `.arsenal/tasks/phase-N/`, `.arsenal/tasks/parallel/`, `.arsenal/tasks/archive/` | Gitignored; phase-N gated per active phase |

**Configuration:** `.arsenal/config.yaml` may override the root location, but defaults work for nearly all projects. File names are not configurable.

**Gating:** `expand-phase` writes baseline denies and per-phase allow rules to `.claude/settings.json`. `close-feature-phase` reverts at phase end. Strategy stays fully denied throughout build.

## Philosophy

- **Lead with the answer.** The exec summary uses SCR (Situation / Complication / Resolution). Every section ends with a "So what?" closure naming the implication.
- **Be honest, not hype.** If the market is saturated or the idea has obvious problems, say so constructively. A weak claim from a Tier-1 source is more credible than a strong claim from a Tier-4 source.
- **Research is uniform; format scales.** Every project gets dispatched parallel research with citations and source-quality grading. The format decision (Brief / Standard / Comprehensive) is made at the end, after research is complete and the user knows what they're looking at.
- **Bottom-up beats top-down for sizing.** Always show bottom-up TAM/SAM/SOM math first; top-down is a sanity check. Investors discount top-down market sizing because it's noise; they trust bottom-up because it forces real assumptions.
- **Citations are non-negotiable.** Every claim has a `[source: URL, tier, confidence]` inline citation. Uncited claims are rejected.

## Output File

| File | Purpose |
|------|---------|
| `.arsenal/strategy/MARKET_RESEARCH.md` | Unified executive research dossier — market + customer + industry structure (with Porter's all 5 forces) + conditional PESTLE + SWOT synthesis + risks + strategic recommendations + appendix (methodology, tier-graded sources, sizing math, confidence summary). Body 3–15 pages depending on format; appendix excluded from page count. |

A working `.arsenal/strategy/RESEARCH_PLAN.md` is produced during research and stays as historical record.

## Workflow

### Step 1: Idea / Project Intake

Open conversation to understand what to research. No rigid questionnaire — adapt to what the user shares.

**Core things to understand:**

- What's the idea / project / market in one sentence?
- Who is it for? (specific — not "everyone" or "developers")
- What problem does it solve? What's the current alternative?
- Why now? Trigger or trend making this timely?
- What's the user's relationship to the project? (founder, investor evaluating an opportunity, strategist scoping adjacent markets, agency doing client research, etc.)
- What's the user's unfair advantage, if any? (domain knowledge, audience, technical skill)
- What does success look like? (revenue, users, strategic insight, investment decision)

**Project intent** drives the output-format recommendation at Step 4d. Capture it explicitly:

- side project / weekend build → likely Brief format
- freelance / small business / early product → likely Standard format
- startup / venture / investor-facing → likely Comprehensive format
- exploring / not sure → default to Standard

Don't gate research effort by intent — research depth is uniform across formats. Intent only informs the format recommendation.

Confirm the intake summary before proceeding.

### Step 2: Market & Customer Research → §1, §2 of `MARKET_RESEARCH.md`

Three sub-phases: **discovery sweep → checkpoint → deep research dispatch**.

#### 2a — Discovery sweep

Quick baseline via inline web search (Jina MCP if available, else WebSearch). Cover:

- Target audience (demographics, psychographics, behaviors)
- Current alternatives (what they use today, including non-obvious ones — spreadsheets, manual)
- Demand signals (search trends, community activity, funding flows in the space)
- Market trajectory (growing / stable / declining)

This is the rough sketch — fast, qualitative. It frames the deeper research questions for 2c. **Don't write the dossier yet.**

#### 2b — Checkpoint

Present the discovery findings concisely (synthesize — don't dump raw search output). Ask:

> *"Here's what I found about [audience / current alternatives / demand signals / trajectory]. Before I go deeper, anything to flag — wrong audience characterization, missing alternatives, threads to prioritize, threads to skip? Or proceed to deep research?"*

Capture flags. **Prompting, not gating** — silence = proceed. The absence of a redirect is approval.

#### 2c — Deep research dispatch

Dispatch structured parallel research via the `dispatch-parallel` skill. This is where the research effort actually goes.

**Step 1 — Write the research plan.** Save to `.arsenal/strategy/RESEARCH_PLAN.md`. 2–5 specific research questions (testable questions, not topics) covering §1 + §2 of the dossier:

- **Q1: Market sizing** — TAM/SAM/SOM, bottom-up calculation with assumptions, top-down sanity check
- **Q2: Customer JTBD** — what jobs (functional, emotional, social) are people hiring solutions for in this space? (Christensen framing)
- **Q3: Pricing benchmarks** — what do alternatives charge? willingness-to-pay anchors?
- **Q4: Demand signals depth** — search trends, community engagement (Reddit, Discord, niche forums), funding flows (Crunchbase, AngelList)
- **Q5: Distribution channels** — where does the target audience discover tools like this?

Adjust to 2–4 questions if some don't apply (e.g., for B2C-only products, skip enterprise-pricing analysis).

For each question, list:
- Priority sources (G2/Capterra, Reddit subreddits, HN, Crunchbase, Indie Hackers, SimilarWeb, SEC filings if applicable, Gartner/Forrester analyst reports if available)
- Source-quality bar (tier-graded T1–T4; see `references/research-dispatch.md`)
- Output requirement: every claim cited inline with `[source: URL, tier: T1–T4, confidence: H/M/L]`

**Step 2 — Dispatch via `dispatch-parallel`.** Hand the plan off. Each research question becomes one investigation.

Constraints:
- Cap: 5 investigations max (dispatch-parallel's hard cap; matches Anthropic's "complex research" scaling rule)
- Surface flag: `--surface web` (research is universal; not platform-specific)
- Per-investigation prompt: **prefix with the framing in `references/research-dispatch.md`** (citation rules + source tiering + tool preferences + strategy + scaling caps), then the specific question and source priorities

Tools available to investigators (in preference order):
1. **Jina MCP** — preferred. `jina_reader` for URL → clean markdown; `jina_search` for query → structured results
2. **Firecrawl** — fallback for JS-heavy sites
3. **WebSearch + WebFetch** — vanilla fallback

Investigators follow research strategy: "start with short broad queries, then narrow"; "prefer primary > authoritative secondary; never SEO content farms." Scaling: 3–10 tool calls per investigation, 15 hard cap.

**Step 3 — Citation pass.** After investigations return, walk the synthesized text. Attach `[source: URL, tier: T1–T4, confidence: H/M/L]` per claim. **Reject any uncited claim** — either re-dispatch the relevant investigation to fill the gap, or remove the claim. Don't fabricate sources.

**Step 4 — Write §1 (Market Overview) and §2 (Customer Analysis) of `MARKET_RESEARCH.md`.** Each section ends with a "So what?" closure (1–2 sentences naming the strategic implication).

§1 (Market Overview):
- Market definition
- Market size: **bottom-up TAM/SAM/SOM first** (show the math + assumptions — investors trust bottom-up), top-down as sanity check, overall confidence H/M/L
- Growth trajectory + demand signals
- **So what?**

§2 (Customer Analysis):
- Target persona(s) with willingness-to-switch indicator (H/M/L)
- Jobs to be done (JTBD) — functional, emotional, social jobs (Christensen)
- Current alternatives (including non-obvious — spreadsheets, manual processes, "ignore the problem")
- Pricing & willingness to pay
- **So what?**

### Step 3: Industry Structure & Competition → §3 (and conditional §4 PESTLE)

Three sub-phases: **competitive landscape → Porter's Five Forces → conditional PESTLE**. §3 is the merge of "competitive analysis" and "structural forces" — Porter's rivalry force IS the competitive landscape; treating them as parallel sections creates redundancy.

#### 3a — Competitive landscape

Three steps: identify → checkpoint → deep analysis dispatch.

**Identify (always).** Find 3–8 direct and indirect competitors via Jina/web search. Present one-line summaries each (URL + what they do + how they monetize). Don't go deep yet — shortlist only.

**Checkpoint:**
> *"Here are [N] competitors I've identified. Which should we dig into? Any to add that I missed (you'd know your space better than search results)? Any to remove because they're not really competing?"*

Capture the adjusted set.

**Deep analysis dispatch.** For the user's adjusted set (cap 5 — if more, prioritize the most relevant or batch into two rounds), dispatch one investigation per competitor via `dispatch-parallel` with the same framing as §2. Each investigation pulls:
- What they do well (with real evidence — feature pages, customer testimonials)
- What they do poorly (G2 / Capterra / Reddit / Twitter complaints — real user voice)
- How they monetize (pricing tiers, business model, conversion mechanics)
- Where the gap is (what they're not doing well that the user can exploit)
- For Comprehensive format: funding/team size (Crunchbase), moat analysis (network effects, integrations, data, brand)

Synthesize into §3.1:
- 2–3 paragraph competitive-landscape narrative (lead with the positioning insight)
- Per-competitor breakdown (compact entries — URL, one-line, strengths, weaknesses, pricing, audience)
- Indirect competitors / substitutes
- Feature comparison matrix (compact table — features as rows, competitors as columns, ✅/❌/🟡)
- Pricing comparison (table — free / paid / enterprise tiers)
- Positioning map (2×2 or spectrum — described in prose with the axes named)

#### 3b — Porter's Five Forces (all 5, relevance-weighted)

All five forces analyzed. **Relevance-weighted by product type** — focus the deepest analysis on the forces that matter most for this product, but every force gets at least 1–2 sentences + a Low/Medium/High rating + 1–2 strategic implications.

For each force, format: **analysis (1–3 sentences) + rating (L/M/H) + implication(s)**.

1. **Competitive Rivalry** — how crowded, how fast competitors move, easy or commoditizing differentiation?
2. **Threat of New Entrants** — what stops someone from cloning this? (network effects, integrations, regulatory, brand, technical complexity)
3. **Threat of Substitutes** — non-obvious alternatives (spreadsheets, manual processes, "ignore the problem"); switching cost?
4. **Bargaining Power of Suppliers** — for software/SaaS, this is non-trivial:
   - Foundation model providers (OpenAI, Anthropic) — pricing changes, deprecations, rate limits
   - Cloud providers (AWS, GCP) — vendor lock-in, egress costs
   - Platform stores (Apple 30%, Google 30%) — review, ranking, ToS changes
   - Payment processors (Stripe 2.9%) — fee changes, account holds
   - Critical APIs (Twilio, SendGrid, etc.) — pricing, deprecation
   Strong supplier power → margin compression, deplatforming risk.
5. **Bargaining Power of Buyers** — depends on segment:
   - Consumer apps: low individual power, aggregated via App Store reviews / rankings
   - B2B SaaS (SMB): moderate — discount expectations, easy to churn
   - B2B SaaS (Enterprise): high — procurement cycles, SOC2 / custom SLAs / MSAs, demand for discounts

**Relevance-weighting guide** — focus the analysis effort on the right forces:

| Product type | Forces with highest signal |
|---|---|
| Consumer mobile app | Substitutes; Suppliers (App Store); Rivalry |
| B2B SaaS (SMB) | Rivalry; New Entrants; Substitutes |
| B2B SaaS (Enterprise) | Buyers (procurement); New Entrants; Suppliers (SSO/auth) |
| AI-powered product | Suppliers (foundation models = your margin); Rivalry; Substitutes |
| Marketplace | Buyers + Suppliers (both sides); New Entrants (network effects) |
| Dev tool | New Entrants; Substitutes (open source); Suppliers (cloud) |
| Platform / API product | Suppliers (your suppliers' ToS); Rivalry; Buyers |

End §3 with a **"So what?"** closure — structural implication for the build. Where's the moat? Where's the wedge? Which force is the biggest threat?

#### 3c — PESTLE macro environment (conditional, §4)

**Only include this section when macro/regulatory forces materially affect the decision.** Specifically: fintech, healthtech, edtech, energy, biotech, automotive, telecom, anything heavily regulated or platform-dependent on government policy.

For everything else, write a 1-line stub in §4: *"Not material — no significant macro/regulatory forces affect this product type."*

When triggered, analyze each:
- **Political / Regulatory** — relevant laws, agencies, policy direction
- **Economic** — recession sensitivity, currency, interest-rate exposure
- **Social** — cultural trends, demographic shifts relevant to the product
- **Technological** — tech shifts amplifying or threatening the idea (AI capabilities, hardware trends, platform changes)
- **Legal** — IP risk, data protection (GDPR / CCPA / HIPAA), contract law specifics
- **Environmental** — sustainability mandates if applicable

**So what?**

### Step 4: Synthesis & Output Format → §5–§7 of `MARKET_RESEARCH.md`

Four sub-phases: **direction check → SWOT → risks → format decision + finalize**.

#### 4a — Direction check with user

Before writing synthesis sections, surface what research revealed and check whether the user wants to reshape any conclusions.

Present 3–5 highest-signal threads, then ask:

> *"Before I finalize the dossier, anything you want to reshape based on what research surfaced? Specifically:*
> - *Target audience or positioning — same as your initial framing, or refine?*
> - *Strongest opportunity — does the threading I described match where you want to focus?*
> - *Anything else to flag before I write the synthesis sections (SWOT, risks, recommendations)?"*

Capture shifts. These inform §5 and §7.

**Prompting, not gating** — silence = proceed.

#### 4b — SWOT synthesis → §5

SWOT pulls from §1, §2, §3 (and §4 if present). 2–3 bullets per quadrant max. **This is integration, not new analysis** — every bullet should reference a finding from earlier sections.

|              | Helpful           | Harmful        |
|--------------|-------------------|----------------|
| **Internal** | Strengths         | Weaknesses     |
| **External** | Opportunities     | Threats        |

- **Strengths:** team/founder advantages (existing skills, audience, domain knowledge, speed)
- **Weaknesses:** gaps (no audience yet, no domain expertise, resource constraints, solo operator limits)
- **Opportunities:** market gaps, timing advantages, underserved segments surfaced in §1 / §3
- **Threats:** competitive responses, platform risks, supplier risk (foundation model pricing, App Store), market shifts, regulatory shifts

End §5 with **strategic posture**: where to attack, where to defend, what to avoid.

#### 4c — Risks & uncertainties → §6

Risk table:

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|

Be honest about known unknowns. Include any T4-source-dependent claims as risks (*"market sizing rests on a single trade-press source; commission primary research before Series A"*).

#### 4d — Output format decision + §7 Strategic Implications → finalize doc

Now the format decision. Ask the user (with a recommendation based on Step 1 intent):

> *"Research and synthesis are done. Now the output format — how compressed should the dossier be? Three options:*
> *- **Brief (3–5 pages body)** — side projects, internal tools, fast validation. Compact §3.1 (top 3 competitors only), Porter's force-by-force depth abbreviated. Headlines, not depth.*
> *- **Standard (5–10 pages body)** — freelance, small business, early-startup. Full §3 (all competitors in the user's adjusted set), all 5 Porter's forces (relevance-weighted depth), JTBD analysis, risk table.*
> *- **Comprehensive (10–15 pages body)** — venture-scale, board / investor audience. Adds methodology disclosure, per-claim confidence ratings, top-down + bottom-up TAM sanity check, full source list, [if PESTLE triggered] full macro analysis.*
> *Recommendation based on your stated intent: [X — see Output Format Guide]. Override?"*

**Page count is body only.** Appendix is excluded — sources can be unbounded; sources are the most important appendix content because they're what makes the doc credible.

Recommendation logic (intent → format):
- side project / weekend build → **Brief**
- freelance / small business → **Standard**
- startup / venture / serious → **Comprehensive**
- exploring / not sure → **Standard** (default; can scale up or down later)

Once format chosen, finalize the dossier. Trim or expand each section to fit the format's page budget.

Write §7 (Strategic Implications & Recommendations) — Pyramid Principle: lead with the answer.

- **Bottom line:** go / pivot / kill (or for non-MVP contexts: "enter / wait / pass"), 1–2 sentences grounded in §1–§6
- **Highest-leverage validations** (before commitment): 2–4 specific actionable items (landing page test, customer interviews, prototype a single screen, primary research engagement, etc.)
- **Most important thing to validate first** — the single most decision-critical unknown

Finalize the **Appendix** (kept brief, excluded from page count, but sources are non-negotiable):

- **A. Methodology** (5–10 lines — research approach, tools used, dates)
- **B. Source list** (most important appendix content) — tier-graded URLs:
  - Tier 1 (primary research, regulatory data)
  - Tier 2 (analyst reports, SEC filings, peer-reviewed)
  - Tier 3 (trade press, industry publications)
  - Tier 4 (blogs, vendor marketing — flagged, used with caution)
- **C. Sizing math** (1–2 paragraphs — show the work for TAM/SAM/SOM)
- **D. Confidence summary** (Comprehensive format only — per-claim confidence H/M/L on the most decision-critical claims)

### Step 5: Handoff

Tell the user where the dossier landed and what natural next steps exist:

- The unified `.arsenal/strategy/MARKET_RESEARCH.md` is the deliverable
- For product validation → `/arsenal-planning:mvp` to drill into the MVP spec (it'll read this dossier as context)
- For investor decks → the Comprehensive-format dossier is itself the artifact; consider extracting the Exec Summary as a one-pager
- For market-entry decisions → the §7 Strategic Implications carries the call

## MARKET_RESEARCH.md template (executive-grade unified)

The exact section structure for the unified research dossier. Section depth scales with format choice (see Output Format Guide).

```markdown
# Market & Competitive Research: [Project Name]

**Date:** [date]
**Format:** [Brief / Standard / Comprehensive]
**Methodology summary:** [1-paragraph — research approach, primary tools, source-tier distribution. Full details in Appendix A.]

## Executive Summary
[1 page — Situation / Complication / Resolution framework. Leads with the answer.

**Situation:** [Market context in 2-3 sentences]
**Complication:** [The strategic question this dossier answers]
**Resolution:** [The recommendation, 1-2 sentences]]

## 1. Market Overview

### 1.1 Market definition
[What market are we in, scoped concretely]

### 1.2 Market size — TAM/SAM/SOM
**Bottom-up estimate (primary):**
[Calculation with assumptions — investors trust bottom-up]

**Top-down sanity check (Standard / Comprehensive):**
[Calculation with source]

**Overall confidence:** [H / M / L] — [why]

### 1.3 Growth trajectory & demand signals
[Growing / stable / declining with quantification; search trends, communities, funding flows]

### So what?
[1-2 sentence implication for the strategic decision]

## 2. Customer Analysis

### 2.1 Target persona(s)
[Primary persona with willingness-to-switch indicator (H/M/L)]

### 2.2 Jobs to be done (JTBD)
[Christensen framing — functional, emotional, social jobs the customer is hiring solutions for]

### 2.3 Current alternatives
[What they use today — including non-obvious ones (spreadsheets, manual processes, "ignore the problem")]

### 2.4 Pricing & willingness to pay
[Benchmarks from competitors; willingness-to-pay anchors from user behavior, surveys, or interview signals]

### So what?

## 3. Industry Structure & Competition

### 3.1 Competitive landscape

[2-3 paragraph narrative leading with the positioning insight]

#### Direct competitors
[Per-competitor breakdown — URL, one-line, strengths, weaknesses, pricing, audience]

#### Indirect competitors / substitutes
[Tools that aren't direct competitors but solve the same underlying job]

#### Feature comparison matrix
| Feature | [Competitor 1] | [Competitor 2] | [Our offering] |
|---|---|---|---|

#### Pricing comparison
| | Free Tier | Paid | Enterprise |
|---|---|---|---|

#### Positioning map
[2×2 or spectrum — describe axes in prose; the map is "high X / low Y" reasoning, not a literal diagram]

### 3.2 Porter's Five Forces (all 5, relevance-weighted)

[Per-force: analysis (1-3 sentences) + rating (L/M/H) + 1-2 strategic implications]

1. **Competitive Rivalry** — [analysis] — Rating: [L/M/H] — Implication: [...]
2. **Threat of New Entrants** — [analysis] — Rating: [L/M/H] — Implication: [...]
3. **Threat of Substitutes** — [analysis] — Rating: [L/M/H] — Implication: [...]
4. **Bargaining Power of Suppliers** — [analysis] — Rating: [L/M/H] — Implication: [...]
5. **Bargaining Power of Buyers** — [analysis] — Rating: [L/M/H] — Implication: [...]

### So what? (structural)
[Where's the moat? Where's the wedge? Which force is the biggest threat?]

## 4. Macro Environment (PESTLE)

[Conditional — full analysis only for regulated / macro-sensitive industries (fintech, healthtech, edtech, energy, biotech, automotive, telecom).
For everything else, this is a 1-line stub:]

*Not material — no significant macro/regulatory forces affect this product type.*

[Full analysis structure when triggered:]

### Political / Regulatory
### Economic
### Social
### Technological
### Legal
### Environmental

### So what?

## 5. SWOT Synthesis

[2-3 bullets per quadrant max. Integration of §1-§4, not new analysis. Each bullet references the source section.]

|              | Helpful           | Harmful        |
|--------------|-------------------|----------------|
| **Internal** | **Strengths**<br>[bullet]<br>[bullet] | **Weaknesses**<br>[bullet]<br>[bullet] |
| **External** | **Opportunities**<br>[bullet]<br>[bullet] | **Threats**<br>[bullet]<br>[bullet] |

### Strategic posture
[Where to attack, where to defend, what to avoid]

## 6. Risks & Uncertainties

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|

## 7. Strategic Implications & Recommendations

**Bottom line:** [go / pivot / kill (or enter / wait / pass) — 1-2 sentences grounded in §1-§6]

**Highest-leverage validations:**
- [Specific actionable item]
- [Specific actionable item]

**Most important thing to validate first:** [The single most decision-critical unknown]

**Recommended next step:** `/arsenal-planning:mvp` to drill into the MVP spec (for product builds) or use this dossier as the artifact (for investor decks, market-entry decisions, strategic planning).

---

## Appendix

[Excluded from page count. Brief but sources are non-negotiable.]

### A. Methodology
[5-10 lines: research approach, tools used (Jina MCP, dispatch-parallel investigations, etc.), date ranges of sources, any primary research done]

### B. Source list (tier-graded)

#### Tier 1 — Primary research, regulatory data
- [URL] — [what it sourced]

#### Tier 2 — Authoritative secondary (analyst reports, SEC filings, peer-reviewed)
- [URL] — [what it sourced]

#### Tier 3 — Reputable industry publications (trade press, established business media)
- [URL] — [what it sourced]

#### Tier 4 — Blogs, vendor marketing (flagged, used with caution)
- [URL] — [what it sourced — and what claim it was the only source for]

### C. Sizing math
[1-2 paragraphs — show the work for TAM/SAM/SOM. Bottom-up assumptions, top-down ratios, confidence on each input.]

### D. Confidence summary (Comprehensive format only)
[Per-claim confidence ratings on the most decision-critical claims — H/M/L with reasoning]
```

## Output Format Guide

| Format | Body length (appendix excluded) | Best for | Recommended when intent is… |
|---|---|---|---|
| **Brief** | 3–5 pages | Side projects, internal tools, fast validation, prototypes | side project, weekend build, just for me, hobby |
| **Standard** | 5–10 pages | Freelance, small business, early-stage startup, MVP validation for personal investment | freelance offering, selling to clients, small business, side project becoming serious |
| **Comprehensive** | 10–15 pages | Venture-scale, board / investor audience, anyone raising or pitching | startup, raising money, $X MRR target, serious about this, investor pitch |

**Section depth scales with format:**

| Section | Brief | Standard | Comprehensive |
|---|---|---|---|
| Executive Summary | 0.5 pg | 1 pg (SCR) | 1-2 pg (SCR + key takeaways callout) |
| §1 Market Overview | Qualitative only; no TAM math | Bottom-up TAM/SAM/SOM | Bottom-up + top-down + confidence intervals |
| §2 Customer Analysis | 1 persona, 1-line JTBD | 1-2 personas, full JTBD framing, pricing benchmarks | Full multi-persona, JTBD with quotes/evidence, willingness-to-pay analysis |
| §3.1 Competitive landscape | Top 3 competitors, compact entries | All competitors in user's adjusted set, full breakdowns, matrix + pricing | Same + funding/team size + moat analysis per competitor |
| §3.2 Porter's Five Forces | All 5 forces, 1-sentence each + rating | All 5, full analysis with relevance-weighting, ratings + implications | Same + per-force evidence citations + scenario analysis |
| §4 PESTLE | Stub only ("not material") unless regulated industry | Stub or full analysis per industry | Full analysis if triggered |
| §5 SWOT | 1-2 bullets/quadrant | 2-3 bullets/quadrant + strategic posture | Same + linked back to §1-§4 evidence per bullet |
| §6 Risks | 2-3 top risks | Full table 5-8 risks | Full table 8-12 risks + mitigation cost |
| §7 Strategic Implications | Bottom line + 1 validation step | + 3 validation steps + most-critical-unknown | + scenario branches (what if [pivot point] is wrong?) |
| Appendix | Sources tier-graded; methodology 3-line | Sources + sizing math + methodology | Sources + sizing + methodology + per-claim confidence |

**Format is decided at Step 4d**, after research is complete and the user knows what they're looking at. Recommendation is intent-based but the user can override.

## Important Guidelines

- **Use real data.** Search the web; use Jina MCP first, then Firecrawl, then WebSearch. Don't invent market stats or fake competitor names. If you can't find a number, say so with confidence L — that's more credible than a fabricated number with confidence H.
- **Be constructively honest.** If the market is crowded, say so — then explain how to differentiate. If the idea is weak, suggest pivots in §7.
- **Connect the dots.** Each section's "So what?" closure is non-negotiable. Without it, the dossier reads as enumeration, not analysis. (Bain's standard pattern.)
- **Separate research from synthesis.** All web searching and data gathering happens in Step 2c and Step 3a via `dispatch-parallel` subagents. The main session synthesizes and writes; it doesn't search inline during synthesis (other than the discovery sweep in 2a / 3a's identify pass).
- **Cite everything.** Every claim in MARKET_RESEARCH.md has a `[source: URL, tier, confidence]` inline citation. Uncited claims are rejected. See `references/research-dispatch.md` for citation format and source-tiering rules.
- **Use Jina MCP first.** Cleaner output, better signal-to-noise than vanilla fetch. The investigator-prompt framing in `references/research-dispatch.md` documents the preference order.
- **Bottom-up beats top-down for sizing.** Always show bottom-up math first; top-down is a sanity check. Investors discount top-down market sizing because it's noise; they trust bottom-up because it forces real assumptions.
- **Body cap is soft; sources are not.** The 3-5 / 5-10 / 10-15 page targets are soft. Going slightly over is fine if the content earns it. But the source list in Appendix B is the credibility anchor — never compress it.
- **This skill is standalone.** It pairs naturally with `mvp` for product validation, but runs equally well for investor decks, market-entry scouting, adjacent-market research, or strategic planning where no MVP follows.
