```
   ▄▀█ █▀█ █▀ █▀▀ █▄ █ ▄▀█ █     █▀█ █   ▄▀█ █▄ █ █▄ █ █ █▄ █ █▀▀
   █▀█ █▀▄ ▄█ ██▄ █ ▀█ █▀█ █▄▄   █▀▀ █▄▄ █▀█ █ ▀█ █ ▀█ █ █ ▀█ █▄█

   ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░
       O U T E R   H E A V E N   T E C H
   ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░
```

A Claude Code plugin. Markdown-only planning skills that turn an idea into a complete planning record — research, specs, UX, design, mockup briefs, GTM:

```
idea → market dossier → MVP spec → feature specs → UX → design → mockup briefs → GTM plan
```

Each skill works standalone. They also chain: each one writes a markdown artifact the next reads, so you never answer the same question twice.

Output is plain markdown in `planning/` and `docs/`. Nothing here writes code, opens PRs, or touches your repo's source tree — that's what the [arsenal-build](https://github.com/Outer-Heaven-Technologies/arsenal-build) plugin is for. **You can use arsenal-planning without arsenal-build.** The artifacts it produces are at canonical paths that any execution system can consume.

## Install

In Claude Code:

```
/plugin marketplace add Outer-Heaven-Technologies/arsenal-planning
/plugin install arsenal-planning@arsenal-planning
```

Then restart Claude Code. The skills register under the `arsenal-planning:` namespace.

### For plugin developers

Fork the repo and edit `SKILL.md` files directly. To get a live-edit loop without reinstalling on every change, symlink your local `skills/` into Claude Code's plugin cache:

```bash
git clone https://github.com/Outer-Heaven-Technologies/arsenal-planning.git ~/Dev/arsenal-planning
ln -sfn ~/Dev/arsenal-planning/skills ~/.claude/plugins/cache/arsenal-planning/arsenal-planning/0.1.0/skills
```

Edits land on the next skill invocation — no reinstall. Add or remove a skill by changing `skills/`; restart Claude Code so it re-scans. Bump the version in `.claude-plugin/plugin.json` when you cut a release.

## How to invoke a skill

Two ways:

1. **Slash command** — `/arsenal-planning:mvp`, `/arsenal-planning:features`, etc. Use when you want to be deliberate.
2. **Natural language** — Claude reads each skill's `description` frontmatter and auto-fires when you say something that matches:
   - "I have an idea for a habit tracker" → `arsenal-planning:mvp`
   - "Drill these features into specs" → `arsenal-planning:features`
   - "Get me a Shopify-style design spec" → `arsenal-planning:design`

Most skills auto-trigger; the per-skill trigger phrases are listed below.

## The pipeline

```
[market-analysis →] mvp → features → ux-{web,app,ios} → design → mockups → [user generates mockups in docs/mockups/] → ──→ arsenal-build:anchor-files → ...
                                                                                                                              gtm
```

> **Research split.** `market-analysis` is an optional upstream skill that produces an executive-grade research dossier (`planning/MARKET_RESEARCH.md` — market + customer + industry structure + Porter's all 5 forces + conditional PESTLE + SWOT + risks + recommendations). It runs standalone for investor decks, market-entry research, and adjacent-market scouting — or feeds into `mvp` for product validation. `mvp` reads it when present and falls back to lightweight inline research when not.

> **Mockup workflow.** `mockups` writes two-pass anchor-strategy briefs to `planning/mockup-briefs/` — copy-paste-ready prompts engineered for Claude Design, Stitch, Open Design, or v0. The user feeds each brief into their tool, saves outputs to `docs/mockups/`. Downstream design execution (in arsenal-build) consumes that directory.

> **Cross-plugin handoff.** Once planning is complete, the natural next step is `arsenal-build:anchor-files` if you have arsenal-build installed (`/plugin install arsenal-build@<repo>`). Otherwise, the artifacts produced here (`MARKET_RESEARCH.md`, `MVP_SPEC.md`, `FEATURES.md` or `features/*.md`, `UX.md`, `DESIGN.md`, `mockup-briefs/`, `GTM_STRATEGY.md`, `REVENUE_MODEL.md`) are at canonical paths for any execution system to consume.

`ux-*` is three surface-specific skills — `ux-web` (marketing sites), `ux-app` (authenticated web apps), `ux-ios` (native iOS). All three write to `docs/UX.md`.

Each stage writes files the next stage reads. You can enter at any point — skills detect upstream artifacts and skip questions whose answers are already on disk.

| # | Skill | What it does |
|---|-------|--------------|
| 1 | [`market-analysis`](#market-analysis--executive-grade-research-dossier) | Executive-grade unified research dossier — market + customer JTBD + industry structure + Porter's all 5 forces + conditional PESTLE + SWOT + risks + strategic recommendations. Standalone or upstream of `mvp`. |
| 2 | [`mvp`](#mvp--mvp-spec-with-go-pivot-kill) | Drill an idea into a focused MVP spec — Must / Should / Won't, success metrics, go/pivot/kill recommendation. Reads `MARKET_RESEARCH.md` if present; otherwise does lightweight inline research. |
| 3 | [`features`](#features--turn-feature-names-into-buildable-specs) | Drill each feature into a buildable spec — user flow, acceptance criteria, states, data model. |
| 4a | [`ux-web`](#ux---ux-architecture-before-visual-design) | `UX.md` for a marketing site/landing page — pages, sections per page, components, conversion model. |
| 4b | [`ux-app`](#ux---ux-architecture-before-visual-design) | `UX.md` for an authenticated web app — app shell, screen inventory, engagement model, empty states. |
| 4c | [`ux-ios`](#ux---ux-architecture-before-visual-design) | `UX.md` for a native iOS app — navigation, onboarding, paywall, permissions, HIG compliance. |
| 5 | [`design`](#design--design-spec-for-any-brand-url-or-written-direction) | Get a `DESIGN.md` — verbatim from catalog (A), extracted from URLs (B), or invented from written direction (C). Library hits return cached. |
| 6 | [`mockups`](#mockups--two-pass-mockup-briefs-for-design-tools) | Generate two-pass anchor-strategy mockup briefs in `planning/mockup-briefs/` from upstream FEATURES + UX + DESIGN. Briefs are copy-paste-ready prompts for Claude Design / Stitch / Open Design / v0. |
| 7 | [`gtm`](#gtm--go-to-market-and-growth-plan) | Plan the launch — positioning, channels, pricing, revenue model, GTM timeline. Runs after the MVP is built (or nearly built). |
| 8 | [`dispatch-parallel`](#dispatch-parallel--fan-out-independent-investigations) | Utility (off-pipeline). Fan out 2–5 genuinely independent investigations to parallel investigator subagents; reconcile results into one `SUMMARY.md`. Read-only; for audits, debug sessions, code analysis. Used internally by `market-analysis`. |

See [`PIPELINE.md`](PIPELINE.md) for the full artifact dependency graph and entry-point matrix.

## Configuration

Tracked artifacts live in `planning/` and `docs/` by default. Override these locations by creating `.arsenal/config.yaml` at the project root:

```yaml
# .arsenal/config.yaml
paths:
  planning: planning/                     # default
  docs: docs/                             # default
  mockups: docs/mockups/                  # default
  mockup_briefs: planning/mockup-briefs/  # default
```

Every skill that reads or writes a tracked artifact performs a preflight check: if `.arsenal/config.yaml` exists, it uses the configured `paths.*` values; if absent, it uses defaults silently — no prompting just to confirm defaults.

| Variable | Default | Holds |
|---|---|---|
| `paths.planning` | `planning/` | MARKET_RESEARCH.md, MVP_SPEC.md, FEATURES.md (or features/*.md), GTM_STRATEGY.md, REVENUE_MODEL.md, RESEARCH_PLAN.md |
| `paths.docs` | `docs/` | UX.md, DESIGN.md |
| `paths.mockups` | `docs/mockups/` | Mockup files (consumed by arsenal-build's design pipeline) |
| `paths.mockup_briefs` | `planning/mockup-briefs/` | Mockup briefs produced by `mockups` |

**File names are not configurable** — only their wrapping directory is. `MVP_SPEC.md` is `MVP_SPEC.md` whether it lives in `planning/` or `arsenal-docs/`.

## Skill details

Each section follows the same shape: a one-line purpose, the steps it runs, how to invoke it, and what it reads and writes.

---

### `market-analysis` — executive-grade research dossier

Produces an **executive-grade unified research dossier** that a CEO or decision-maker could read end-to-end and make a strategic call from. Standalone — runs for product validation (upstream of `mvp`), investor decks, market-entry research, adjacent-market scouting, or any project requiring grounded strategic analysis.

**How it works**

1. **Intake.** Open conversation to understand what to research. Captures project intent (hobby / freelance / startup / venture / strategist evaluating an opportunity) — drives the format recommendation at Step 4d.
2. **Discovery sweep + checkpoint.** Quick baseline web search, then a checkpoint where the user can flag threads to pursue or skip.
3. **Deep research dispatch.** 2–5 parallel investigations via `dispatch-parallel` covering market sizing (bottom-up TAM/SAM/SOM), customer JTBD, pricing benchmarks, demand signals, and per-competitor deep analysis. Uses Jina MCP / Firecrawl / WebSearch. Every claim tier-graded (T1–T4) and cited with URL + confidence.
4. **Industry structure.** Porter's all 5 forces, relevance-weighted by product type (AI products focus on Supplier Power; enterprise SaaS focuses on Buyer Power). Conditional PESTLE for regulated industries (fintech, healthtech, etc.).
5. **Direction check + SWOT + format decision.** Synthesize highest-signal threads, ask if the user wants to reshape any conclusions, then SWOT into the dossier and pick an output format (Brief / Standard / Comprehensive) with an intent-based recommendation.
6. **Final dossier.** Writes `planning/MARKET_RESEARCH.md` with §7 Strategic Implications + Appendix (methodology, tier-graded source list, sizing math, optional per-claim confidence summary).

**Unified dossier structure:** Exec Summary (SCR — Situation/Complication/Resolution) → §1 Market Overview (bottom-up TAM/SAM/SOM) → §2 Customer Analysis (JTBD per Christensen) → §3 Industry Structure & Competition (landscape + Porter's all 5 forces) → §4 PESTLE (conditional) → §5 SWOT → §6 Risks → §7 Strategic Implications → Appendix.

**Output format scales (decided at end, not start):**

| Format | Body length | Recommended for |
|---|---|---|
| Brief | 3–5 pp | Side projects, internal tools, fast validation |
| Standard | 5–10 pp | Freelance, small business, early-stage startup, MVP validation |
| Comprehensive | 10–15 pp | Venture-scale, board / investor audience, anyone raising or pitching |

Appendix is excluded from page count — sources (Appendix B) are the credibility anchor and always kept complete.

**How to use it**

- **Slash command:** `/arsenal-planning:market-analysis`
- **Or trigger with:** "research the market for…", "do a competitive analysis for…", "validate this market", "build me a market dossier", "executive research on…", "should I enter this space"

- **Inputs:** none — pure upstream.
- **Outputs (`planning/`):** `MARKET_RESEARCH.md` (unified executive dossier). A working `RESEARCH_PLAN.md` is produced during research and stays as historical record.

---

### `mvp` — MVP spec with go/pivot/kill

Drills an idea into a focused **MVP spec** — what to build first, why, with success metrics and a go/pivot/kill recommendation. Reads `planning/MARKET_RESEARCH.md` from `market-analysis` if present; otherwise does lightweight inline research to ground the spec.

**How it works**

1. **Intake.** Idea, audience, problem, intent (hobby / freelance / startup / **client-work**), surface (web / iOS / mobile / CLI / etc.). If client-work signal, soft-routes to `/arsenal-planning:features` directly with the client brief.
2. **Research context.** Three paths:
   - **A.** `MARKET_RESEARCH.md` exists from `market-analysis` → reads the dossier and uses it as context.
   - **B.** No dossier; project is non-trivial → suggests running `market-analysis` first, with continue/lightweight/skip options.
   - **C.** Lightweight inline research (5–10 web queries via Jina MCP or WebSearch) — grounding-grade, not dossier-grade.
3. **Direction check.** If research surfaced anything (Path A or C), briefly ask the user whether their understanding has shifted before drafting the spec.
4. **Write the spec.** `planning/MVP_SPEC.md` with Must / Should / Won't feature buckets, user stories, core value loop, success metrics, distribution hypothesis, phased roadmap.
5. **Review & recommendation.** Go / pivot / kill in conversation, with reasoning grounded in research if present, in intake judgment otherwise (and flagged accordingly).

**Surface-level tech decisions are in scope** — web vs iOS, mobile vs desktop, marketing site vs authenticated webapp — those shape the spec and feed downstream skills. Stack-specific decisions (framework, database, hosting) defer to `arsenal-build:anchor-files`.

**How to use it**

- **Slash command:** `/arsenal-planning:mvp`
- **Or trigger with:** "I have an idea for…", "should I build…", "validate this idea", "is this worth building", "scope out an MVP"

- **Inputs:** `planning/MARKET_RESEARCH.md` (optional — from `market-analysis`).
- **Outputs (`planning/`):** `MVP_SPEC.md`.

---

### `features` — turn feature names into buildable specs

Drills each feature in a list into a spec an engineer or coding agent can implement without guessing — job story, user flow, Given/When/Then acceptance criteria, states, data model, dependencies, plus an `Important` section for counter-intuitive boundaries the agent would otherwise miss (e.g. "don't add gamification mechanics").

**How it works**

1. **Source & mode.** Pulls feature list from `MVP_SPEC.md` or user input. Picks output mode: **single-file** (1–5 features) or **split-file** (6+).
2. **Style.** Sequential, draft-and-redline, or hybrid drilling.
3. **Drill.** Concrete-choice questions instead of open-ended ones. Surfaces edge cases, traces the data path. Every question gets answered or punted to `Important` — there is **no "Open Questions" bucket**.
4. **Write.** One spec per feature.
5. **Reconcile.** Updates `MVP_SPEC.md` if drilling reshuffled Must/Should/Won't.

Split-file mode is built to pair with downstream build skills (in arsenal-build): a `Read(planning/features/*)` deny rule in `.claude/settings.json` lets the controller opt into one feature spec at a time. With a single combined file, the deny rule is all-or-nothing.

**How to use it**

- **Slash command:** `/arsenal-planning:features`
- **Or trigger with:** "drill down on these features", "spec out the features", "lock down the specs", "define each feature"

Communicates like a senior PM in a 1:1 — one question at a time.

- **Inputs:** `planning/MVP_SPEC.md` (optional) or a user-provided list.
- **Outputs:** `planning/FEATURES.md` (single mode) **or** `planning/features/<slug>.md` per feature plus `planning/features/README.md` index (split mode).

---

### `ux-*` — UX architecture before visual design

Writes `docs/UX.md` — the UX backbone of whatever surface you're building. Three surface-specific skills share the contract:

| Skill | Surface | Triggers on |
|---|---|---|
| `ux-web` | Marketing sites, landing pages, agency portfolios, waitlists — anything public-facing measured in signups/leads/purchases | "plan the landing page", "scaffold the marketing site", "what sections does this homepage need", "structure the agency site" |
| `ux-app` | Authenticated web apps — SaaS app surface, dashboards, internal tools, productivity apps | "plan the app UX", "scaffold the dashboard", "what screens does this app need", "lay out the app shell" |
| `ux-ios` | Native iOS apps (SwiftUI, RN-iOS, Flutter-iOS) | "plan the iOS UX", "scaffold the iPhone app", "design the onboarding flow", "plan the paywall" |

All three follow the same UX/UI separation: **UX.md owns *what goes where*; DESIGN_SYSTEM owns *how it looks*.** And all three end at the same artifact, `docs/UX.md`, so downstream skills don't need to know which variant produced it.

For hybrid products (SaaS with marketing + app, iOS with marketing site), run two of them sequentially.

**How they work**

1. **Discover.** Read upstream planning context (`MVP_SPEC.md`, `ARCHITECTURE.md`, `CLAUDE.md`) before asking.
2. **Classify.** `ux-app` classifies app shape (productivity, daily-driver, dashboard, creation, etc.) and engagement model (ritual, workflow, reference, creation, monitoring). `ux-ios` classifies app shape and monetization model (free, freemium, subscription, hard/soft paywall). `ux-web` classifies industry against its skeleton library.
3. **Lookup library.** Each skill consults its own references:
   - `ux-web/references/skeletons.md` — industry skeletons (SaaS, Fintech, AI/Chatbot, DevTool, Agency, Micro SaaS) + compressed appendix
   - `ux-app/references/app-patterns.md` — app shell, engagement models, screen patterns, anti-patterns
   - `ux-ios/references/ios-patterns.md` — navigation, onboarding 9-step, paywall compliance, permissions, plus iOS industry skeletons (habit tracker, meditation, mood tracker)
4. **Generate.** Customize the reference for the specific product. Anti-patterns are the highest-signal part — high-stakes pages/screens get at least 3–5 each.
5. **Cross-check.** Reconcile against any existing `DESIGN_SYSTEM.md` and report components that need definitions.

**Skill-specific concerns**

- **`ux-web`** applies a "2026 conversion sequence baseline" (stop the scroll → earn trust → explain value → remove doubt → make the ask) as a sanity check.
- **`ux-app`** decides the app shell first — sidebar vs top-nav, persistent banner, Cmd-K, global quick-create — because those decisions cascade through every screen. Empty states get first-class treatment.
- **`ux-ios`** designs the onboarding flow (9-step framework), paywall strategy with Apple compliance check, and permission timing per moment of value. Cross-checks against HIG.

**How to use them**

- **Slash commands:** `/arsenal-planning:ux-web`, `/arsenal-planning:ux-app`, `/arsenal-planning:ux-ios`
- **Or trigger by phrasing** — each skill's description lists explicit triggers (see table above)

- **Inputs:** optional `MVP_SPEC.md`, `FEATURES.md`, plus the per-skill references library.
- **Outputs:** `docs/UX.md` (all three). Long output splits into a parent `UX.md` plus per-page sub-files under `docs/ux/`.

---

### `design` — design spec for any brand, URL, or written direction

Produces a `DESIGN.md` and maintains a personal library at `~/.claude/design-md-library/<slug>/`. Three paths:

- **Path A — catalog.** Verbatim fetch of a curated DESIGN.md from VoltAgent's getdesign.md catalog. *"Get me Shopify."*
- **Path B — extract.** Faithfully extracts a design from one or more URLs. URLs *are* the source. *"Extract design from northsignal.dev."*
- **Path C — inspiration.** Invents an original DESIGN.md from written direction, with optional URL refs as mood boards (not sources). *"Moody brutalist, late-90s zine, but warmer."*

**The B-vs-C line.** B reproduces existing designs faithfully; C creates new ones using refs for inspiration. Written direction or blend language ("X meets Y", "inspired by X but Z") routes to C. Pure URLs with faithful intent route to B. When a single message contains both, the skill asks once.

**How it works**

1. **Library check.** Looks up by slug; hits return verbatim.
2. **Route.** Phrasing routes among A / B / C — see the trigger list below.
3. **Path A — fetch.** `curl` raw markdown from `unpkg`. The skill explicitly forbids scraping the React-rendered `getdesign.md` HTML; Firecrawl is the fallback.
4. **Path B — extract.** `firecrawl_scrape` with tuned parameters (`onlyMainContent=false`, `waitFor=2000`, fullpage screenshot), then *studies the site like a designer, not a parser* — atmosphere and typographic personality first, tokens second.
5. **Path C — invent.** Parses written direction (atmosphere, archetype, hard constraints), scrapes any ref URLs as *mood boards* not sources, then writes an original DESIGN.md. Adds an **Influences** subsection to Section 1 citing each ref and what it contributed; tokens get inline citations only where the link to a ref is real and specific.
6. **Stage & promote.** Output staged to `/tmp/design/<slug>/`, then atomically `mv`'d into the library (never `cp` — the library stays the single source of truth).
7. **Preview.** Generates light/dark HTML previews.

**Verbatim discipline.** Path A and library hits are byte-for-byte. *If the source has 9 sections, yours has 9. If it says 'Near Black,' yours says 'Near Black.'* Paths B and C are the only paths that produce new writing — B faithful to URLs, C invented from refs. Both pick **one** closest-match reference example (claude / apple / lamborghini) per run; Path C may read two if the direction explicitly mixes archetypes ("warm but loud"). Inferred values get tagged `[inferred]` with reasoning. Saying "refresh", "re-extract", or "force new" re-runs against the saved `source.txt`.

**Path C refresh caveat.** Refreshing a Path C entry re-invents from saved direction and refs — output won't be byte-identical to the previous version. The skill warns before regenerating.

**How to use it**

- **Slash command:** `/arsenal-planning:design`
- **Or trigger with:**
  - **Path A:** "get me Shopify", "make a DESIGN.md like Apple"
  - **Path B:** "extract design from northsignal.dev", "I want my UI to look like <site>"
  - **Path C:** "inspired by X", "design that feels like X meets Y", "moody brutalist with these refs"
  - **Refresh:** "refresh the saved <brand> design"

- **Inputs:** brand name, URL(s), or written direction (with optional ref URLs/phrases); references in the skill directory.
- **Outputs:** `<slug>/DESIGN.md`, `preview.html`, `preview-dark.html`, `source.txt` — promoted to `~/.claude/design-md-library/<slug>/`. `source.txt` records `path: catalog | extract | inspiration` plus the URLs or direction needed for refresh.

---

### `mockups` — two-pass mockup briefs for design tools

Generates copy-paste-ready mockup briefs in `planning/mockup-briefs/` engineered for Claude Design, Stitch, Open Design, or v0. Applies a **two-pass anchor strategy**: lock the visual language with one or two anchor screens first, then derive every other screen against the locked anchor.

The skill writes briefs only — it does not generate mockups itself. The user feeds each brief into their tool of choice and saves the output to `docs/mockups/`. A future `generate-mockups` skill (planned around `nexu-io/open-design`, will live in arsenal-build) will consume these same briefs automatically.

**Why two passes**

Most teams generate inconsistent mockups by going straight to per-feature screens. The mockup tool has to re-derive the visual language each time. The anchor strategy fixes this by separating the "lock the look" decision from the "place this screen's content" decision.

| Pass | Goal | Iterations expected |
|---|---|---|
| **1 — Anchor** | Establish the visual language (typography hierarchy, spacing rhythm, color application, navigation shape) | 5–15 |
| **2 — Derived** | Place per-screen content into the locked language | 1–3 per screen |
| **3 — States** | Variants for empty / loading / error / first-run | 1 per state |

Pass 1 is the highest-leverage prompt in the project. Pass 2 is mostly mechanical once Pass 1 is right.

**How it works**

1. **Verify preconditions.** Hard-requires `planning/FEATURES.md` (or split features), `docs/UX.md`, `docs/DESIGN.md`. Missing → routes to the right arsenal-planning skill.
2. **Classify surface + select anchor(s).** Marketing site → homepage. Web app → dashboard. Native iOS → home/today + onboarding step 1 (two anchors — iOS has two visual languages).
3. **Generate Pass 1 brief(s).** Full DESIGN.md + UX.md anchor section, no feature content yet. Skill stops and prompts the user to feed the brief into their tool, save the anchor mockup to `docs/mockups/`, return.
4. **Generate Pass 2 briefs (after Pass 1 locked).** Per high-stakes screen — references the anchor as visual reference, pulls relevant feature spec visual sections (User Flow, States, Important, Data — skips Acceptance Criteria and Dependencies).
5. **Generate Pass 3 briefs (optional).** State variants for screens with meaningful state coverage in FEATURES § States.
6. **Index + handoff.** Writes `planning/mockup-briefs/README.md` with workflow order and status tracking.

**Tool-specific guidance** ships in `references/worked-examples.md` — same Pass 1 brief shown three ways (Claude Design / Stitch / Open Design), plus tool quickstart matrix, Pass 2 + Pass 3 worked examples, and common iteration patterns when output drifts.

**How to use it**

- **Slash command:** `/arsenal-planning:mockups`
- **Or trigger with:** "generate mockup briefs", "prepare mockups", "draft mockup prompts", "script mockups", "I need mockups for this product"

- **Inputs (hard-required):** `planning/FEATURES.md` or `planning/features/`, `docs/UX.md`, `docs/DESIGN.md`.
- **Outputs:** `planning/mockup-briefs/README.md` (index) + `planning/mockup-briefs/01-anchor-<screen>.md` (Pass 1) + `planning/mockup-briefs/02-<screen>.md` (Pass 2) + `planning/mockup-briefs/03-<screen>-<state>.md` (Pass 3, optional).

---

### `gtm` — go-to-market and growth plan

Picks up after the MVP is built (or nearly built) and produces a research-backed launch plan — positioning, channels, pricing validation, revenue modeling, marketing budget, week-by-week launch timeline, and a KPI dashboard with **decision triggers** so the operator doesn't think from scratch every month.

**How it works**

A 7-step (0–6) workflow with its own Lean / Moderate / Deep depth tiers. (Note: `mvp` switched to output-format tiers — Brief / Standard / Comprehensive — decided at the end. `gtm` retains the traditional depth-tier model since GTM planning genuinely has different effort levels per tier.)

0. **Context.** Reads existing planning docs, scans the codebase, asks key questions — including the operator's **AI tooling stack**, which feeds directly into channel sizing (*a solo dev with AI agents, automation, and smart workflows can operate at 3–5x capacity*).
1. **Position & channel.** 2–4 primary channels, each paired with an explicit AI-leverage play.
2. **Revenue model.** Three scenarios (conservative / moderate / aggressive), each reverse-engineered from MRR target → users → conversion → CPA → CLV → churn ceiling.
3. **Budget & unit economics.** Marketing spend breakdown, CLV:CAC ≥3:1 target, payback period, per-channel kill thresholds.
4. **Launch calendar.** Pre-launch / launch-week / post-launch.
5. **KPIs.** Pre-committed decision triggers ("if MRR growth below X% for 2 months, revisit channel strategy") plus a monthly review template.
6. **Recommend.** Single highest-leverage next action.

**Honest math, not vibes.** *If the revenue target requires 50K monthly visitors at a 3% conversion rate and they have no audience, say that clearly.* A "Channels Explicitly Not Pursuing (and why)" section guards against distraction. Sensitivity analysis is built in. Same no-tech-decisions rule as `mvp` — recommends *strategies*, not tools.

**How to use it**

- **Slash command:** `/arsenal-planning:gtm`
- **Or trigger with:** "how do I launch this", "how do I get my first users", "what's my marketing plan", "help me price this", "I built the MVP, now what?"

- **Inputs:** prior planning docs (`MVP_SPEC.md`, `MARKET_RESEARCH.md`, `ARCHITECTURE.md` if present). If absent, the skill asks for the MVP spec and market research.
- **Outputs (`planning/`):** `GTM_STRATEGY.md`, `REVENUE_MODEL.md`.

---

### `dispatch-parallel` — fan out independent investigations

A utility skill, **off the linear pipeline**. Dispatches 2–5 read-only investigations to parallel investigator subagents and reconciles their results into a single `SUMMARY.md` with cross-investigation overlap detection and severity-tagged recommendations. Use for audits, market research fan-outs, debug sessions, code analysis — any case where the work is genuinely disjoint and parallelization actually pays for itself.

Lives in arsenal-planning because `market-analysis` depends on it. Equally useful in build contexts when arsenal-build is also installed — the skill is read-only and stack-agnostic.

**The independence gate is the whole skill.** Before any dispatch, it checks three criteria — disjoint scope, no shared mutations, result-independence — and refuses to fan out if they don't hold. Failing the gate is the success case for dependent work; the skill recommends sequential execution.

**Does NOT compose with code-changing pipelines.** Investigations are read-only by design — investigator subagents have zero write capability. When findings recommend code changes, the SUMMARY's "Next steps" block points the user at the per-task pipelines (`arsenal-build:run-task-{web,ios}` for sequential, one-fix-at-a-time work) or the orchestrators (`arsenal-build:features-{web,ios}` for pattern-spanning work as a TASKS.md phase). The two plugins connect through filesystem and user judgment, not direct invocation.

**Locked contracts:**
- **Count bounds:** N = 1 refuses with suggestion; 2 ≤ N ≤ 5 normal; N ≥ 6 hard-refuses (recommend phase modeling).
- **Idempotence:** default skip per-investigation if `investigation-{N}-result.md` exists; `--force` regenerates.
- **Conflict handling:** when investigations contradict each other, SUMMARY flags conflicts in a `## Conflicts requiring user resolution` section and the skill exits cleanly.
- **Investigator tools:** broad read (Read / Glob / Grep / read-only Bash / WebSearch / Firecrawl / claude-in-chrome), zero write.

**How to use it**

- **Slash command:** `/arsenal-planning:dispatch-parallel`
- **Or trigger with:** "investigate these in parallel", "fan out on these issues", "run these checks concurrently", "audit X, Y, and Z separately"

- **Inputs:** 2–5 investigation descriptions via `--investigation` (repeated) or `--from-file <path>`, optional `--surface web|ios` for codebase context, optional `--force`, optional `--max <N>` (capped at 5).
- **Outputs (`.tasks/parallel/<run-id>/`):** `investigation-N-result.md` per investigation (≤3k tokens), `SUMMARY.md` (aggregated with overlap detection + conflict flags + next-step recommendations).

## File layout

```
arsenal-planning/
├── .claude-plugin/
│   └── plugin.json              # plugin manifest (name: arsenal-planning)
├── skills/
│   ├── market-analysis/
│   │   ├── SKILL.md
│   │   └── references/research-dispatch.md
│   ├── mvp/SKILL.md
│   ├── features/SKILL.md
│   ├── ux-web/
│   │   ├── SKILL.md
│   │   └── references/skeletons.md
│   ├── ux-app/
│   │   ├── SKILL.md
│   │   └── references/app-patterns.md
│   ├── ux-ios/
│   │   ├── SKILL.md
│   │   └── references/ios-patterns.md
│   ├── design/
│   │   ├── SKILL.md
│   │   └── references/{template,example-claude,...}.md
│   ├── mockups/
│   │   ├── SKILL.md
│   │   └── references/worked-examples.md
│   ├── gtm/SKILL.md
│   └── dispatch-parallel/
│       ├── SKILL.md
│       └── references/investigator-prompt.md
├── PIPELINE.md
├── LICENSE
└── README.md
```

`SKILL.md` is the contract — frontmatter (`name`, `description`) plus body. Claude reads `description` to decide when to auto-invoke; the body is what actually runs.

## Philosophy

- **Resolve, don't catalog.** Specs end without "TBD" or "open questions." Every question gets either an answer or an explicit punt. Ambiguity is the enemy of LLM-assisted development.
- **Match depth to stakes.** A weekend side project gets a lean pass. A venture gets the deep dive. The skills read the signals and adapt.
- **Markdown only.** Planning artifacts are cheap to throw away and cheap to iterate. The expensive stuff (code, deploys) belongs to arsenal-build.

## Pairs with

- **[arsenal-build](https://github.com/Outer-Heaven-Technologies/arsenal-build)** — the execution half. Consumes the planning artifacts produced here and writes real code, opens PRs.

## Credits

The `design` skill is built around [VoltAgent's awesome-design-md](https://github.com/VoltAgent/awesome-design-md) project (MIT licensed). The 9-section DESIGN.md format, the catalog of curated brand templates, and the `getdesign.md` website are all VoltAgent's work. Arsenal's `design` skill is original code that orchestrates fetches from VoltAgent's public catalog and writes new DESIGN.md files in their format — but the format spec belongs to them. Thanks to the awesome-design-md maintainers.

## License

[AGPL-3.0-or-later](LICENSE). Use arsenal commercially in your workflow — the artifacts it produces are yours. You can't repackage *the tool itself* as a closed-source product.

<details>
<summary>Full license summary</summary>

**You can freely:**

- Use arsenal in Claude Code as part of your workflow, including for paid client work or building your own commercial product. The artifacts arsenal produces (planning docs, code, designs) are yours — they aren't covered by the license.
- Modify the skills for your own use.
- Share modifications publicly under AGPL.

**The AGPL kicks in when you redistribute the skills themselves:**

- Wrapping arsenal's prompts/skills in a UI and selling it as a SaaS or product → you must release your source under AGPL.
- Forking arsenal and shipping a paid plugin built from it → same.
- Embedding the `SKILL.md` content (modified or not) into a commercial product → same.

For a commercial license that allows proprietary derivatives, contact Outer Heaven Technologies.

</details>
