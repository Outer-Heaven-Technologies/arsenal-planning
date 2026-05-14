---
name: mvp
description: Drills an idea into a focused MVP spec — what to build first, why, with success metrics and a go/pivot/kill recommendation. Reads upstream `.arsenal/strategy/MARKET_RESEARCH.md` from `market-analysis` if available; otherwise does lightweight inline research to ground the spec. Captures project intent (hobby / freelance / startup / client-work); client-work soft-routes to `features`. Surface-level tech decisions (web vs iOS, mobile vs desktop) are fair game — stack-specific decisions defer to `anchor-files`. Outputs `.arsenal/strategy/MVP_SPEC.md`. Use when the user has an idea and wants a buildable spec — with or without prior market analysis.
---

# Plan MVP

Drill an idea into a focused **MVP spec** — what to build first, why, with clear success metrics and a `go / pivot / kill` recommendation.

This skill produces `.arsenal/strategy/MVP_SPEC.md`. It works two ways:

- **With upstream research** — if `.arsenal/strategy/MARKET_RESEARCH.md` exists (from `market-analysis`), this skill uses it as context. The spec lands grounded in the executive dossier's findings.
- **Without upstream research** — does lightweight inline research (5–10 web queries, no formal citation rigor) to validate basic assumptions before writing the spec. Faster, less rigorous than running `market-analysis` first, but enough to draft an informed spec.

If you want both — full executive dossier AND focused MVP spec — run `market-analysis` first, then this skill picks up where it left off. For just the spec (client work, internal tools, fast scoping), run this skill standalone.

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

- **Validate before building.** Kill bad ideas early; sharpen good ones.
- **Lightweight when standalone, grounded when not.** If `MARKET_RESEARCH.md` exists from `market-analysis`, lean on it. Otherwise do enough research to draft an informed spec — but don't reinvent the executive dossier.
- **Ruthlessly cut scope.** MVP = smallest thing that validates the core hypothesis. If it's not in the core loop, it's post-MVP.
- **Be honest, not hype.** If the idea has obvious problems, say so constructively.
- **Surface-level tech decisions are fine.** Web vs iOS, mobile vs desktop, marketing site vs webapp — those shape the spec and shouldn't be deferred. Stack-specific decisions (framework, database, hosting) defer to `/arsenal-build:anchor-files`.

## Output File

| File | Purpose |
|------|---------|
| `.arsenal/strategy/MVP_SPEC.md` | Feature scope (Must / Should / Won't), user stories, core value loop, success metrics, phased roadmap |

## Workflow

### Step 1: Idea Intake

Open conversation. Let the user describe the idea naturally; ask clarifying questions adaptively (no rigid questionnaire).

**Core things to understand:**

- What's the idea in one sentence?
- Who is it for? (specific — not "everyone" or "developers")
- What problem does it solve? What's the current alternative?
- Why now? Trigger or trend making this timely?
- Is this a side project, freelance offering, startup, **client work** (commissioned build for a paying client), or internal tool?
- What surface — web (marketing site / authenticated webapp), native iOS, mobile (cross-platform), CLI, library, server-only? This shapes the spec and downstream skills (ux-*, design-*).
- What's the user's unfair advantage? (domain knowledge, audience, technical skill)
- What does success look like? (revenue, users, personal use, portfolio)

**If the user signals client work**, soft-route them out:

> *"For client work, this skill is usually skipped — the build decision is already made, validation isn't yours to do, and the brief already exists. Recommended path: go straight to `/arsenal-planning:features` with the client brief as input — that skill drills each feature into a buildable spec. Want to continue here anyway (light scoping doc), or stop and run features?"*

Confirm scope and intake summary before proceeding.

### Step 2: Research Context

Check for upstream research. Three paths.

#### Path A — `.arsenal/strategy/MARKET_RESEARCH.md` exists

The user has already run `market-analysis`. Read the dossier — particularly §1 (Market Overview), §2 (Customer Analysis), §3 (Industry Structure & Competition), §7 (Strategic Implications). Use these to ground the spec. Skip Path B and C.

#### Path B — No upstream research; user wants the dossier

If no `MARKET_RESEARCH.md` exists and the project is non-trivial (anything beyond a quick weekend tool), prompt:

> *"No `MARKET_RESEARCH.md` from market-analysis found. For [serious product / startup / something you're betting time and money on], running `/arsenal-planning:market-analysis` first is strongly recommended — it produces an executive-grade dossier (TAM/SAM/SOM, customer JTBD, competitive landscape, Porter's all 5 forces, SWOT) that grounds the MVP spec in real research. Options:*
> *- Run `/arsenal-planning:market-analysis` first (recommended for serious projects)*
> *- Continue here with **lightweight inline research** (faster, less rigorous — I'll do a quick search to validate basic assumptions before writing the spec)*
> *- Skip research entirely (use only your intake answers; viable for prototypes and obvious-fit ideas)"*

If the user picks `market-analysis` first → stop, hand off, re-enter after dossier is produced (Path A).

#### Path C — Lightweight inline research

If the user picks "lightweight research" or the project is small enough to not warrant a full dossier (hobby projects, internal tools, fast prototypes), do **5–10 inline web queries** to validate basic assumptions:

- Is there demand for this? (one search — search trends, related communities)
- Who are 2–3 direct competitors? (one or two searches — find them, skim their landing pages)
- What's the rough pricing in the space? (one search — competitor pricing pages)
- Any obvious blockers? (one search — regulatory landmines, platform restrictions, recent shutdowns of similar products)

Use Jina MCP if available, otherwise WebSearch. **This is grounding-grade research, not dossier-grade** — no formal citation requirements, no source-tier grading, no dispatch-parallel. The goal is enough context to draft an informed spec, not a research dossier.

Synthesize findings into a brief mental model (don't write a research file). If something surfaces that meaningfully changes the spec (e.g., a near-identical competitor just shipped the same idea, or there's a regulatory issue the user didn't know about), flag it before proceeding.

### Step 3: Direction Check (only if research context exists)

If Path A (full dossier) or Path C (lightweight research) surfaced anything, briefly check with the user before writing the spec:

> *"Quick check before drafting the spec — does anything from research shift your understanding? Target audience, positioning, the core feature priority? Or proceed as discussed?"*

For Path A users (full dossier), reference 1–2 specific findings from `MARKET_RESEARCH.md`. For Path C users (lightweight research), reference what you found that's most decision-relevant.

Capture any pivots. These shape the spec.

**Prompting, not gating** — silence = proceed. Skip this step entirely if user picked "skip research" in Step 2.

### Step 4: Write MVP_SPEC.md

Write `.arsenal/strategy/MVP_SPEC.md` using the template below. The spec reflects the **current** understanding (post-Step-3 shifts), not the original Step 1 intake.

**Principles:**

- MVP = the smallest thing that validates the core hypothesis
- Every feature ties back to a user problem identified in intake / research
- Ruthlessly cut scope — if it's not in the core loop, it's post-MVP
- Include clear success metrics so the user knows if the MVP worked
- Surface-level tech decisions (web / iOS / etc.) belong here. Stack-level decisions don't — those go in `anchor-files`.

**Format: `.arsenal/strategy/MVP_SPEC.md`**

```markdown
# MVP Spec: [Project Name]

**Date:** [date]
**One-liner:** [what it is in one sentence]

## Core Hypothesis
[What are we trying to validate? Frame as: "We believe [target user] will [action] because [reason]"]

## Target User
[Refined from intake / §2 of MARKET_RESEARCH.md if exists — one specific persona for MVP]

## Surface
[Web (marketing site / authenticated webapp), native iOS, cross-platform mobile, CLI, library, server-only. This is the platform-level decision; stack details defer to anchor-files.]

## Core Value Loop
[The single repeating loop that delivers value. E.g., "User logs habit → sees streak → feels motivated → comes back tomorrow"]

## MVP Feature Set

### Must Have (Launch Blockers)
- [ ] [Feature] — [why it's essential, ties to which user need]
- [ ] [Feature] — [why]

### Should Have (Week 2–4 post-launch)
- [ ] [Feature] — [why]

### Won't Have (Explicitly Deferred)
- [ ] [Feature] — [why it's tempting but not MVP]

## User Stories
[3–5 key user stories in "As a [user], I want to [action] so that [outcome]" format]

## Monetization Strategy (if applicable)
[Free? Freemium? Paid? When does money enter the picture?]

## Distribution Hypothesis
[How will the first 100 users find this? Be specific — not "social media" but "post Show HN + cross-post to r/selfhosted". Name 1–2 primary channels and explain why for this specific audience. Sanity check that there's a path to early users.]

## Success Metrics
| Metric | Target | Timeframe |
|--------|--------|-----------|
| [e.g., Daily active users] | [e.g., 50] | [e.g., 30 days post-launch] |
| [e.g., Retention D7] | [e.g., 30%] | [e.g., 30 days post-launch] |

## Risks & Mitigations
| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| [risk] | H/M/L | H/M/L | [what to do] |

## Phased Roadmap

### Phase 1: MVP (Weeks 1-X)
[Core features, launch goal]

### Phase 2: Validate (Weeks X-Y)
[Iterate based on metrics, add Should Haves]

### Phase 3: Scale (Weeks Y-Z)
[Growth features, monetization, expansion]

## Upstream Research
[If `.arsenal/strategy/MARKET_RESEARCH.md` was the source for this spec, reference it here: "Grounded in `.arsenal/strategy/MARKET_RESEARCH.md` — see §X for [topic]". Otherwise note "Spec drafted from intake + lightweight research" or "Spec drafted from intake only — recommend running `/arsenal-planning:market-analysis` before significant investment".]

## Next Step
→ For UI projects: `/arsenal-planning:features` to drill each MVP feature, then `ux-{web,app,ios}` → `design` → `mockups` → `/arsenal-build:anchor-files` → per-phase build pipelines.
→ For non-UI projects (CLI, library, API, server-only): skip ux-* and design, go `/arsenal-planning:features` → `/arsenal-build:anchor-files` directly.
```

### Step 5: Review & Recommendation

After `MVP_SPEC.md` lands, give the user a tight closing summary in conversation:

- **Go / pivot / kill** — 1–2 sentences with the reasoning
  - If Path A (full dossier): reference the dossier's §7 recommendation; check whether the spec's scope is consistent with it
  - If Path B/C (lightweight or no research): make the call based on intake judgment + whatever the lightweight research surfaced; flag explicitly that this is a lower-confidence call than a full dossier-grounded one
- **Most important thing to validate first** — the single most decision-critical unknown
- **Quick validation steps** before writing code (landing page test, Reddit post, DM 10 potential users, prototype a single screen)
- **Natural next step:** `/arsenal-planning:features` for UI projects (then ux-* → design → mockups → anchor-files), or `/arsenal-build:anchor-files` directly for non-UI projects
- **After MVP is built:** `/arsenal-planning:gtm` is the logical step

If the user skipped research entirely (Step 2 option 3) and the project looks serious in hindsight, soft-suggest running `market-analysis` retroactively as a sanity check before significant investment.

## Important Guidelines

- **Lightweight research is grounding, not dossier-grade.** When this skill does its own research in Step 2 Path C, the goal is enough context to draft an informed spec — not to produce an executive dossier. That's `market-analysis`'s job.
- **If the project is serious, point at `market-analysis`.** Don't try to do `market-analysis`-grade work inline. Suggest it; let the user choose.
- **Surface-level tech decisions are fine.** Web vs iOS, mobile vs desktop, CMS-backed vs static — those shape the MVP and shouldn't be deferred. The line is at framework/database/hosting choice, which is `anchor-files`'s job.
- **Ruthlessly cut scope.** Must Have should be the smallest possible set that validates the core hypothesis. When in doubt, defer.
- **Use real research signals.** Even lightweight research means real web searches, not speculation. If you can't find demand signals, say so — that itself is a finding.
- **Connect the spec to research.** If `MARKET_RESEARCH.md` exists, every section of the spec should reference it where applicable (Target User → §2, Risks → §6, Distribution → §1.3). The spec is a synthesis, not an independent doc.
- **Cap MVP_SPEC.md at ~400 lines.** Push exploratory content into `MARKET_RESEARCH.md` (or accept it lives there from a prior `market-analysis` run). The spec stays tight and actionable.
