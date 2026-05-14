---
name: gtm
description: Builds a research-backed go-to-market plan covering positioning, channel strategy, pricing, revenue modeling, marketing budget, and launch timeline. Adapts to operator stage from solo first-100-users to startup MRR targets. Use after the MVP is built (or nearly so) when the user needs a launch plan, pricing validation, or growth strategy.
---

# Plan GTM

Build a research-backed launch and growth plan for a product that's been built (or is nearly complete). This skill picks up where `/arsenal-planning:mvp` and `/arsenal-build:setup` leave off — it answers "how do I get this in front of people and make money?"

## Paths

All arsenal artifacts live under `.arsenal/` at the project root.

| What | Path | Notes |
|---|---|---|
| Strategy archive (denied during build) | `.arsenal/strategy/` | MVP_SPEC.md, mockup-briefs/, GTM_STRATEGY.md, REVENUE_MODEL.md, research/{MARKET_RESEARCH,RESEARCH_PLAN}.md |
| Feature specs | `.arsenal/FEATURES.md` (single-mode) or `.arsenal/features/<slug>.md` (split-mode) | Gated per phase via `.claude/settings.json` |
| Project anchor docs | `.arsenal/{ARCHITECTURE,CONVENTIONS,TASKS}.md` | Always readable during build |
| Design reference set | `.arsenal/design/{UX,DESIGN,DESIGN_SYSTEM}.md` + `.arsenal/design/mockups/` | Always readable during build |
| Per-task briefs + ephemera | `.arsenal/tasks/phase-N/`, `.arsenal/tasks/parallel/`, `.arsenal/tasks/archive/` | Gitignored; phase-N gated per active phase |

**Configuration:** `.arsenal/config.yaml` may override the root location, but defaults work for nearly all projects. File names are not configurable.

**Gating:** `expand-phase` writes baseline denies and per-phase allow rules to `.claude/settings.json`. `close-feature-phase` reverts at phase end. Strategy stays fully denied throughout build.

## Philosophy

- **Research-grounded, not vibes.** Every channel recommendation, pricing decision, and revenue target should be backed by market data or competitor signals.
- **Founder-practical.** Assume the operator is a solo dev or tiny team until told otherwise. Recommend what one person can actually execute.
- **Honest math.** If the revenue target requires 50K monthly visitors at a 3% conversion rate and they have no audience, say that clearly.
- **Adapt to stage.** A side project getting its first 100 users needs a different playbook than a startup targeting $50K MRR.

## Output Files

| File | Purpose |
|------|---------|
| `.arsenal/strategy/GTM_STRATEGY.md` | Positioning, channels, pricing, launch plan |
| `.arsenal/strategy/REVENUE_MODEL.md` | Revenue scenarios, unit economics, growth projections |

Files go in the same `.arsenal/strategy/` directory used by `/arsenal-planning:mvp`. If those docs exist, reference them — don't redo the research.

## Workflow

### Step 0: Lift strategy lockdown (if applicable), then gather context

**First, lift the strategy lockdown if it's in place.** When build execution has started in this project, `expand-phase` will have written `Read(.arsenal/strategy/**)` to `.claude/settings.json` to deny strategy access during build. `gtm` legitimately needs `MARKET_RESEARCH.md` and `MVP_SPEC.md` from there — so:

1. Read `.claude/settings.json`. If `permissions.deny` contains `Read(.arsenal/strategy/**)`, **remove that entry temporarily**. Preserve any non-arsenal entries the user added.
2. Write back.
3. Tell the user: *"Lifting `.arsenal/strategy/` lockdown for GTM research. Will restore at the end."*

After all GTM work is done (final output written), **restore the strategy deny** by re-adding `Read(.arsenal/strategy/**)` to `permissions.deny` and writing settings back. Tell the user: *"Strategy lockdown restored."*

If `.claude/settings.json` doesn't exist or doesn't have a strategy deny, this is a pre-execution invocation — proceed without mutation; strategy is freely readable.

**Then, gather context.** Look for existing project context — this skill builds on prior work:

**If running in Claude Code (has filesystem access):**
- Check for `.arsenal/strategy/research/MARKET_RESEARCH.md` (unified executive dossier from `/arsenal-planning:market-analysis` — contains market overview, customer JTBD, competitive landscape, Porter's Five Forces, SWOT, and recommendations all in one doc; competitive analysis lives in §3) and `.arsenal/strategy/MVP_SPEC.md` from `/arsenal-planning:mvp`
- Check for `ARCHITECTURE.md`, `CONVENTIONS.md` from `/arsenal-build:setup`
- Scan the codebase to understand what's actually been built (routes, features, UI)
- Use all of this as input context — don't ask the user to repeat what's already documented

**If running elsewhere (no filesystem access):**
- Ask the user to provide: the MVP spec, any market research or competitive analysis they've done, and a brief description of what they've built
- If they have the planning docs from `/arsenal-planning:mvp`, ask them to paste or attach the relevant ones

**Always ask the user (regardless of context available):**
- What's your revenue goal? (If they don't have one, that's fine — you'll propose scenarios in Step 2)
- What's your timeline? (When do you want to launch? When do you need revenue?)
- What's your marketing budget? ($0 is a valid answer — organic-first is a real strategy)
- Do you have an existing audience? (newsletter, social following, community, client base)
- What are you willing to do personally? (write content, do sales calls, run ads, build in public)
- What AI tools and automation do you use or are willing to use? (e.g., Claude Code, AI writing tools, social scheduling, email automation, workflow agents, etc.)

The AI tooling question matters because it directly affects channel capacity. A solo operator with strong AI leverage can realistically run 3-4 channels that would traditionally require a small team. Factor the user's AI stack into channel recommendations — don't artificially limit them to 1-2 channels if they have the tooling to run more.

### Step 1: Positioning & Channel Strategy

Use web search to validate positioning and identify the right channels. Don't guess — research where the target audience actually hangs out and what messaging resonates.

**Positioning:**
- Refine the value proposition from the MVP spec into launch-ready messaging
- Define the one-sentence pitch, the elevator pitch (30 sec), and the landing page headline
- Position relative to competitors identified in the competitive analysis
- Identify the "wedge" — the specific angle that differentiates from incumbents

**Channel Strategy:**
Research and recommend 2-4 primary acquisition channels based on where the target audience is. Scale channel count based on the user's AI tooling — more automation = more channels are viable. For each channel:
- Why this channel fits this audience (with evidence — e.g., "r/productivity has 2.1M members and weekly threads about task management tools")
- What the playbook looks like (specific actions, not vague advice)
- **AI leverage play:** How AI tools can automate or accelerate this channel (e.g., "use AI to generate 5 post variants per topic, A/B test hooks" or "use agents to monitor competitor mentions and draft responses")
- Expected cost (time and/or money — note reduced time cost where AI handles the work)
- Timeline to see results
- How to measure if it's working

**Channel selection should be grounded in research, not generic advice.** Don't just say "use Twitter and SEO." Research whether the target audience is actually on Twitter. Check if SEO is viable given the competitive landscape. Look at what channels successful competitors used early on.

Common channels to evaluate (pick the 2-3 that actually fit):
- Community-led (Reddit, Discord, forums, Slack groups)
- Content/SEO (blog, YouTube, newsletter)
- Social (Twitter/X, LinkedIn, TikTok — depends entirely on audience)
- Marketplaces (Product Hunt, App Store, Chrome Web Store, etc.)
- Outbound (cold email, DMs, partnerships)
- Paid (Google Ads, Meta Ads, sponsorships)
- Build in public (Twitter threads, indie hacker communities)

### Step 2: Revenue Modeling

This is where the math happens. The goal is to give the user realistic scenarios so they can set informed targets.

**Research phase:**
- Pull pricing data from competitors (identified in competitive analysis or via new research)
- Look for public revenue signals (indie hackers posts, OpenStartup pages, SimilarWeb traffic estimates)
- Identify pricing norms for the category (per-seat, usage-based, flat monthly, freemium)

**Build three revenue scenarios:**

Present the user with conservative, moderate, and aggressive scenarios. Each scenario should include:

| | Conservative | Moderate | Aggressive |
|---|---|---|---|
| **Monthly price point** | [based on market floor] | [based on market median] | [based on premium positioning] |
| **Month 6 users** | [realistic organic growth] | [organic + some paid] | [organic + paid + viral/referral] |
| **Month 6 MRR** | $ | $ | $ |
| **Month 12 MRR** | $ | $ | $ |
| **Required monthly growth rate** | X% | X% | X% |

**For each scenario, reverse-engineer the acquisition math:**
- Monthly new users needed to hit the MRR target
- Required conversion rate (free → paid, or visitor → customer)
- Implied cost per acquisition (CPA) that keeps unit economics healthy
- Customer lifetime value (CLV) needed to justify the CPA
- Churn rate ceiling (the max churn that still allows growth)

**After presenting scenarios, ask the user:** "Which scenario aligns with your goals, or do you have a different target in mind?" Then build the detailed model from their chosen target.

### Step 3: Budget & Unit Economics

Based on the chosen revenue scenario, build a practical budget.

**Marketing budget breakdown by month (months 1-6):**
- What to spend on each channel
- Expected return per channel
- When to scale spend vs. stay organic
- Clear "kill threshold" for each paid channel (e.g., "if CPA exceeds $X after 2 weeks, pause and reassess")

**Unit economics validation:**
- CLV:CAC ratio (target 3:1+ for sustainable growth)
- Payback period (how many months to recoup acquisition cost)
- Gross margin per customer
- Break-even point (users and revenue)

**If the budget is $0:**
That's fine — build an organic-first plan with time investment estimates instead of dollar amounts. Be explicit about the tradeoff: organic is cheaper but slower, and estimate the timeline difference.

### Step 4: Launch Plan

Build a concrete, week-by-week launch timeline.

**Pre-launch (2-4 weeks before):**
- Landing page / waitlist setup
- Content pipeline (what to publish and where)
- Community seeding (where to start showing up before launch)
- Beta user recruitment strategy

**Launch week:**
- Day-by-day launch sequence
- Which platforms to hit and in what order
- Specific post templates / outreach scripts (adapted to the user's voice)
- How to handle the spike (support, onboarding, feedback collection)

**Post-launch (weeks 2-8):**
- Feedback loop cadence (how often to ship, how to collect feedback)
- Channel doubling-down criteria (what signals tell you a channel is working)
- When to start paid acquisition (if applicable)
- Retention plays (onboarding emails, feature announcements, community building)

### Step 5: KPI Dashboard

Define the metrics that matter for tracking progress toward the revenue target.

**Weekly tracking metrics:**
- New signups / downloads
- Activation rate (% who complete core action)
- Revenue (MRR, new MRR, churned MRR)
- Channel-specific metrics (traffic by source, conversion by source)

**Monthly review metrics:**
- MRR growth rate vs. target
- CLV:CAC ratio trend
- Churn rate
- Net revenue retention

**Decision triggers:**
- "If MRR growth is below X% for 2 consecutive months, revisit channel strategy"
- "If churn exceeds X%, pause acquisition and fix retention"
- "If a channel's CPA exceeds $X, reallocate budget to top performer"

### Step 6: Deliver & Recommend

Present the two output docs and provide:
- The single highest-leverage action to take this week
- The biggest risk to the plan and how to mitigate it
- A 30/60/90 day checkpoint schedule
- Remind them that these projections need revisiting after launch with real data — the model is a starting hypothesis, not a promise

## Format: `.arsenal/strategy/GTM_STRATEGY.md`

```markdown
# Go-to-Market Strategy: [Project Name]

**Date:** [date]
**Revenue Target:** [chosen scenario — e.g., $10K MRR by month 6]
**Marketing Budget:** [monthly budget or "organic-first"]

## Positioning
### One-Sentence Pitch
[what it is, who it's for, why it's different]

### Elevator Pitch (30 seconds)
[expanded version with the hook, problem, solution, differentiation]

### Landing Page Headline
[the headline that goes above the fold]

### Competitive Position
[how this product is positioned relative to alternatives — reference competitive analysis]

## Channel Strategy

### Primary Channel: [Channel Name]
- **Why:** [evidence-based reasoning]
- **Playbook:** [specific actions]
- **AI Leverage:** [how AI tools handle the heavy lifting for this channel]
- **Cost:** [time and/or money, noting where AI reduces time investment]
- **Timeline:** [when to expect results]
- **Success metric:** [how to know it's working]

### Secondary Channel: [Channel Name]
[same structure]

### Channels Explicitly Not Pursuing (and why)
[important to document what you're NOT doing so you don't get distracted]

## Launch Plan

### Pre-Launch (Weeks -4 to -1)
[week-by-week actions]

### Launch Week
[day-by-day sequence]

### Post-Launch (Weeks 2-8)
[cadence, feedback loops, scaling triggers]

## KPI Dashboard
| Metric | Week 1 Target | Month 1 Target | Month 3 Target | Month 6 Target |
|--------|--------------|----------------|----------------|----------------|
| [metric] | [target] | [target] | [target] | [target] |

## Decision Triggers
[if X happens, do Y — pre-committed responses to common scenarios]

## Next Steps
[the 3 things to do this week]
```

## Format: `.arsenal/strategy/REVENUE_MODEL.md`

```markdown
# Revenue Model: [Project Name]

**Date:** [date]
**Chosen Scenario:** [Conservative / Moderate / Aggressive / Custom]

## Pricing Strategy
- **Model:** [freemium / flat monthly / per-seat / usage-based / etc.]
- **Price point(s):** [with reasoning from competitive research]
- **Free tier:** [what's free, what's paid, and why the line is drawn there]

## Revenue Scenarios Overview
| | Conservative | Moderate | Aggressive |
|---|---|---|---|
| Monthly price | $ | $ | $ |
| Month 3 MRR | $ | $ | $ |
| Month 6 MRR | $ | $ | $ |
| Month 12 MRR | $ | $ | $ |
| Required monthly growth | X% | X% | X% |

## Chosen Scenario: Detailed Breakdown

### Acquisition Math
| Month | New Users Needed | Cumulative Users | Conversion Rate | Paying Customers | MRR |
|-------|-----------------|------------------|-----------------|------------------|-----|
| 1 | | | | | $ |
| 2 | | | | | $ |
| ... | | | | | $ |
| 12 | | | | | $ |

### Unit Economics
- **Customer Lifetime Value (CLV):** $ [with assumptions]
- **Customer Acquisition Cost (CAC):** $ [by channel]
- **CLV:CAC Ratio:** X:1
- **Payback Period:** X months
- **Gross Margin:** X%
- **Break-even:** X paying customers / $X MRR

### Marketing Budget by Month
| Month | Channel 1 | Channel 2 | Total Spend | Expected New Customers | Blended CPA |
|-------|-----------|-----------|-------------|----------------------|-------------|
| 1 | $ | $ | $ | | $ |
| ... | | | | | |

### Sensitivity Analysis
[What happens if churn is 2x expected? If conversion rate is half? If CPA doubles? Show the user which assumptions matter most.]

## Key Assumptions
[List every assumption that the model depends on. Be explicit — this is the section the user revisits after launch with real data.]

## Monthly Review Template
At the end of each month, revisit:
1. Actual MRR vs. projected MRR
2. Actual CPA vs. projected CPA
3. Actual churn vs. projected churn
4. Which assumptions held? Which broke?
5. What to adjust for next month
```

## Adaptive Depth Guide

| Signal | Depth | Scope |
|--------|-------|-------|
| "side project", "hobby", "just want some users" | Lean | Simple pricing, 1-2 organic channels, basic revenue estimate, skip detailed budget |
| "freelance tool", "small business", "want to make money" | Moderate | Pricing validation, 2-3 channels, revenue scenarios, light budget |
| "startup", "serious revenue goal", "$X MRR target" | Deep | Full pricing analysis, 3+ channels with playbooks, detailed revenue model, budget breakdown, unit economics |

## Important Guidelines

- **Use real data.** Search the web for pricing benchmarks, traffic estimates, channel effectiveness data. Don't invent conversion rates.
- **Be honest about the math.** If the user's target requires unrealistic growth rates or conversion rates, say so. Propose an achievable alternative.
- **Build on existing work.** If `/arsenal-planning:mvp` docs exist, reference them heavily. Don't redo market research or competitive analysis — extend it into actionable GTM.
- **No tech decisions.** Don't recommend analytics tools, email platforms, or ad tech. Recommend *strategies*. The user will pick their own tools.
- **Respect solo operator constraints — but factor in AI leverage.** A solo dev without tooling can't run 5 channels. But a solo dev with AI agents, automation, and smart workflows can operate at 3-5x capacity. During intake, assess the user's AI stack and adjust channel recommendations accordingly. For each channel, note where AI can handle the heavy lifting (e.g., "use AI to draft weekly newsletter content, you review and send" or "use Claude to generate 10 LinkedIn post variants, schedule the best 3"). The goal is to maximize output per hour of the operator's attention, not just minimize the number of channels.
- **Pre-commit to decisions.** The decision triggers and monthly review template exist so the user doesn't have to think from scratch every month. Help them decide in advance what they'll do when things go right or wrong.
- **Separate research from conclusions.** Gather all pricing data, channel research, and competitor signals before building the revenue model. Don't interleave searching with modeling — the numbers should be grounded in complete data, not partial findings.
- **Research with precision, not just volume.** For moderate and deep projects, ground every number in the revenue model with real data. Crawl competitor pricing pages directly rather than guessing from search snippets. Read Product Hunt launch posts to see what channels worked for similar products. Check SimilarWeb or public traffic estimates to validate channel viability. Search for founder retrospectives ("how I got my first 1000 users" posts in the same space). Look at actual ad cost data (Google Keyword Planner, Facebook ad library) when paid channels are in scope. Use multiple queries per assumption when the first results are thin — the revenue model is only as good as the data feeding it.
- **Cap output files at ~500 lines.** Long markdown bloats downstream context and becomes unscannable. Sweet spot: 100–400 lines. If `GTM_STRATEGY.md` would exceed 500 lines, split by concern (e.g., `GTM_STRATEGY.md` overview + `CHANNELS.md` per-channel playbooks + `PRICING.md`). Keep `REVENUE_MODEL.md` focused — push detailed scenarios into a sibling `REVENUE_SCENARIOS.md` if needed and cross-link.
