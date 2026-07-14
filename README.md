```
   ▄▀█ █▀█ █▀ █▀▀ █▄ █ ▄▀█ █     █▀█ █   ▄▀█ █▄ █ █▄ █ █ █▄ █ █▀▀
   █▀█ █▀▄ ▄█ ██▄ █ ▀█ █▀█ █▄▄   █▀▀ █▄▄ █▀█ █ ▀█ █ ▀█ █ █ ▀█ █▄█

   ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░
       O U T E R   H E A V E N   T E C H
   ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░
```

A Claude Code plugin. Markdown-only planning skills that turn an idea into a complete planning record — research, specs, UX, design, and GTM:

```
idea → market dossier → MVP spec → feature specs → UX → design → GTM plan
```

Each skill works standalone. They also chain: each one writes a markdown artifact the next reads, so you never answer the same question twice.

Output is plain markdown under `.arsenal/` at your project root. Nothing here writes code, opens PRs, or touches your repo's source tree — that's what the [arsenal-build](https://github.com/Outer-Heaven-Technologies/arsenal-build) plugin is for. **You can use arsenal-planning without arsenal-build.** The artifacts it produces are at canonical paths that any execution system can consume.

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
ln -sfn ~/Dev/arsenal-planning/skills ~/.claude/plugins/cache/arsenal-planning/arsenal-planning/0.3.0/skills
```

Edits land on the next skill invocation — no reinstall. Add or remove a skill by changing `skills/`; restart Claude Code so it re-scans. Bump the version in `.claude-plugin/plugin.json` when you cut a release.

## How to invoke a skill

Two ways:

1. **Slash command** — `/arsenal-planning:mvp`, `/arsenal-planning:features`, etc. **Required for `mvp`, `features`, and `gtm`** — these produce a multi-file planning suite, so opting in is deliberate.
2. **Natural language** — Claude reads each skill's `description` frontmatter and auto-fires when you say something that matches:
   - "Plan the iOS UX" → `arsenal-planning:ux-ios`
   - "Get me a Shopify-style design spec" → `arsenal-planning:design`
   - "Validate this market" → `arsenal-planning:market-analysis`

`mvp`, `features`, and `gtm` require slash commands. The other skills auto-trigger; per-skill trigger phrases are listed below and in PIPELINE.md.

## The pipeline

```
[market-analysis →] mvp → features → ux-{web,app,ios} → design → ──→ arsenal-build → ...
                                                                         gtm
```

> **Research split.** `market-analysis` is an optional upstream skill that produces an executive-grade research dossier (`.arsenal/strategy/research/MARKET_RESEARCH.md` — market + customer + industry structure + Porter's all 5 forces + conditional PESTLE + SWOT + risks + recommendations). It runs standalone for investor decks, market-entry research, and adjacent-market scouting — or feeds into `mvp` for product validation. `mvp` reads it when present and falls back to lightweight inline research when not.

> **Mockups.** Put actual mockup source or rendered files in `.arsenal/design/mockups/`. The build orchestrator inspects available mockups alongside FEATURES, UX, and DESIGN when planning each phase.

> **Cross-plugin handoff.** Once planning is complete, hand the project to arsenal-build. The artifacts produced here (`MARKET_RESEARCH.md`, `MVP_SPEC.md`, `FEATURES.md` or `features/*.md`, `UX.md`, `DESIGN.md`, `GTM_STRATEGY.md`, `REVENUE_MODEL.md`, and optional actual mockups) are at canonical paths under `.arsenal/` for any execution system to consume.

`ux-*` is three surface-specific skills — `ux-web` (marketing sites), `ux-app` (authenticated web apps), `ux-ios` (native iOS). All three write to `.arsenal/design/UX.md`.

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
| 6 | [`gtm`](#gtm--go-to-market-and-growth-plan) | Plan the launch — positioning, channels, pricing, revenue model, GTM timeline. Runs after the MVP is built (or nearly built). |

See [`PIPELINE.md`](PIPELINE.md) for the full artifact dependency graph and entry-point matrix.

## Artifact layout

All arsenal artifacts live at fixed canonical paths under `.arsenal/` at the project root:

| Path | Holds |
|---|---|
| `.arsenal/strategy/` | User archive (MARKET_RESEARCH, RESEARCH_PLAN, MVP_SPEC, GTM_STRATEGY, REVENUE_MODEL). **Denied during build execution.** |
| `.arsenal/FEATURES.md` (single-mode) or `.arsenal/features/` (split-mode) | Feature specs. Gated per phase. |
| `.arsenal/{ARCHITECTURE,CONVENTIONS,TASKS}.md` | Project anchor docs. `TASKS.md` is the single task ledger. Always readable during build. |
| `.arsenal/design/{UX,DESIGN,DESIGN_SYSTEM}.md` + `.arsenal/design/mockups/` | Design reference set and actual mockups. Always readable during build. |

## Build-time access

During build execution, `.arsenal/strategy/` is protected and out-of-scope split feature specs should remain unread unless the active phase cites them. The build orchestrator reads the active feature specs, the design reference set, and the single `.arsenal/TASKS.md` ledger. Host-specific permission rules may enforce those boundaries, but the artifact structure does not depend on a particular provider or settings-file format.

## Skill details

Each section follows the same shape: a one-line purpose, the steps it runs, how to invoke it, and what it reads and writes.

---

### `market-analysis` — executive-grade research dossier

Produces an **executive-grade unified research dossier** that a CEO or decision-maker could read end-to-end and make a strategic call from. Standalone — runs for product validation (upstream of `mvp`), investor decks, market-entry research, adjacent-market scouting, or any project requiring grounded strategic analysis.

**How it works**

1. **Intake.** Open conversation to understand what to research. Captures project intent (hobby / freelance / startup / venture / strategist evaluating an opportunity) — drives the format recommendation at Step 4d.
2. **Discovery sweep + checkpoint.** Quick baseline web search, then a checkpoint where the user can flag threads to pursue or skip.
3. **Deep research.** Runs 2–5 bounded investigations covering market sizing (bottom-up TAM/SAM/SOM), customer JTBD, pricing benchmarks, demand signals, and per-competitor deep analysis. Uses fresh-context workers in parallel when the host supports them and an equivalent sequential inline fallback otherwise. Every claim is tier-graded (T1–T4) and cited with URL + confidence.
4. **Industry structure.** Porter's all 5 forces, relevance-weighted by product type (AI products focus on Supplier Power; enterprise SaaS focuses on Buyer Power). Conditional PESTLE for regulated industries (fintech, healthtech, etc.).
5. **Direction check + SWOT + format decision.** Synthesize highest-signal threads, ask if the user wants to reshape any conclusions, then SWOT into the dossier and pick an output format (Brief / Standard / Comprehensive) with an intent-based recommendation.
6. **Final dossier.** Writes `.arsenal/strategy/research/MARKET_RESEARCH.md` with §7 Strategic Implications + Appendix (methodology, tier-graded source list, sizing math, optional per-claim confidence summary).

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
- **Outputs (`.arsenal/strategy/`):** `MARKET_RESEARCH.md` (unified executive dossier). A working `RESEARCH_PLAN.md` is produced during research and stays as historical record.

---

### `mvp` — MVP spec with go/pivot/kill

Drills an idea into a focused **MVP spec** — what to build first, why, with success metrics and a go/pivot/kill recommendation. Reads `.arsenal/strategy/research/MARKET_RESEARCH.md` from `market-analysis` if present; otherwise does lightweight inline research to ground the spec.

**How it works**

1. **Intake.** Idea, audience, problem, intent (hobby / freelance / startup / **client-work**), surface (web / iOS / mobile / CLI / etc.). If client-work signal, soft-routes to `/arsenal-planning:features` directly with the client brief.
2. **Research context.** Three paths:
   - **A.** `MARKET_RESEARCH.md` exists from `market-analysis` → reads the dossier and uses it as context.
   - **B.** No dossier; project is non-trivial → suggests running `market-analysis` first, with continue/lightweight/skip options.
   - **C.** Lightweight inline research (5–10 web queries via Jina MCP or WebSearch) — grounding-grade, not dossier-grade.
3. **Direction check.** If research surfaced anything (Path A or C), briefly ask the user whether their understanding has shifted before drafting the spec.
4. **Write the spec.** `.arsenal/strategy/MVP_SPEC.md` with Must / Should / Won't feature buckets, user stories, core value loop, success metrics, distribution hypothesis, phased roadmap.
5. **Review & recommendation.** Go / pivot / kill in conversation, with reasoning grounded in research if present, in intake judgment otherwise (and flagged accordingly).

**Surface-level tech decisions are in scope** — web vs iOS, mobile vs desktop, marketing site vs authenticated webapp — those shape the spec and feed downstream skills. Stack-specific decisions (framework, database, hosting) defer to arsenal-build's first-run setup.

**How to use it**

- **Slash command:** `/arsenal-planning:mvp`
- **Or trigger with:** "I have an idea for…", "should I build…", "validate this idea", "is this worth building", "scope out an MVP"

- **Inputs:** `.arsenal/strategy/research/MARKET_RESEARCH.md` (optional — from `market-analysis`).
- **Outputs (`.arsenal/strategy/`):** `MVP_SPEC.md`.

---

### `features` — turn feature names into buildable specs

Drills each feature in a list into a spec an engineer or coding agent can implement without guessing — job story, user flow, Given/When/Then acceptance criteria, states, data model, dependencies, plus an `Important` section for counter-intuitive boundaries the agent would otherwise miss (e.g. "don't add gamification mechanics").

**How it works**

1. **Source & mode.** Pulls feature list from `MVP_SPEC.md` or user input. Picks output mode: **single-file** (1–5 features) or **split-file** (6+).
2. **Style.** Sequential, draft-and-redline, or hybrid drilling.
3. **Drill.** Concrete-choice questions instead of open-ended ones. Surfaces edge cases, traces the data path. Every question gets answered or punted to `Important` — there is **no "Open Questions" bucket**.
4. **Write.** One spec per feature.
5. **Reconcile.** Updates `MVP_SPEC.md` if drilling reshuffled Must/Should/Won't.

Split-file mode lets the build orchestrator cite and load only the feature specs relevant to the active phase, keeping task expansion focused while preserving stable canonical paths.

**How to use it**

- **Slash command:** `/arsenal-planning:features`
- **Or trigger with:** "drill down on these features", "spec out the features", "lock down the specs", "define each feature"

Communicates like a senior PM in a 1:1 — one question at a time.

- **Inputs:** `.arsenal/strategy/MVP_SPEC.md` (optional) or a user-provided list.
- **Outputs:** `.arsenal/FEATURES.md` (single mode) **or** `.arsenal/features/<slug>.md` per feature plus `.arsenal/features/README.md` index (split mode).

---

### `ux-*` — UX architecture before visual design

Writes `.arsenal/design/UX.md` — the UX backbone of whatever surface you're building. Three surface-specific skills share the contract:

| Skill | Surface | Triggers on |
|---|---|---|
| `ux-web` | Marketing sites, landing pages, agency portfolios, waitlists — anything public-facing measured in signups/leads/purchases | "plan the landing page", "scaffold the marketing site", "what sections does this homepage need", "structure the agency site" |
| `ux-app` | Authenticated web apps — SaaS app surface, dashboards, internal tools, productivity apps | "plan the app UX", "scaffold the dashboard", "what screens does this app need", "lay out the app shell" |
| `ux-ios` | Native iOS apps (SwiftUI, RN-iOS, Flutter-iOS) | "plan the iOS UX", "scaffold the iPhone app", "design the onboarding flow", "plan the paywall" |

All three follow the same UX/UI separation: **UX.md owns *what goes where*; DESIGN_SYSTEM owns *how it looks*.** And all three end at the same artifact, `.arsenal/design/UX.md`, so downstream skills don't need to know which variant produced it.

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
- **Outputs:** `.arsenal/design/UX.md` (all three). Long output splits into a parent `UX.md` plus per-page sub-files under `.arsenal/design/ux/`.

---

### `design` — design spec for any brand, URL, or written direction

Produces a `DESIGN.md` in the **[Google DESIGN.md format](https://github.com/google-labs-code/design.md)** (Apache 2.0) — YAML front matter holding machine-readable design tokens plus an 8-section markdown body — and maintains a personal library at `~/.claude/design-md-library/<slug>/`. Three paths:

- **Path A — catalog.** Fetches a curated source from VoltAgent's getdesign.md catalog, then **transforms** the 9-section source into Google's 8-section + YAML format. The catalog is the brand-truth source; we normalize the shape. Original kept at `<slug>/legacy/DESIGN.md` for reference. *"Get me Shopify."*
- **Path B — extract.** Faithfully extracts a design from one or more URLs, emitting directly in Google format. URLs *are* the source. *"Extract design from northsignal.dev."*
- **Path C — inspiration.** Invents an original DESIGN.md from written direction (Google format), with optional URL refs as mood boards (not sources). *"Moody brutalist, late-90s zine, but warmer."*

**Mandatory lint gate.** Every emit runs through `npx @google/design.md@latest lint` before promotion to the library. Errors block; warnings surface for triage. No file ships invalid.

**The B-vs-C line.** B reproduces existing designs faithfully; C creates new ones using refs for inspiration. Written direction or blend language ("X meets Y", "inspired by X but Z") routes to C. Pure URLs with faithful intent route to B. When a single message contains both, the skill asks once.

**How it works**

1. **Library check.** Looks up by slug; hits return the saved DESIGN.md verbatim.
2. **Route.** Phrasing routes among A / B / C — see the trigger list below.
3. **Path A — fetch + transform.** `curl` raw markdown from `unpkg`, save VoltAgent source to `<slug>/source/voltagent.md`, then transform section-by-section into Google format (YAML tokens extracted from prose; 9 sections collapsed to 8).
4. **Path B — extract.** `firecrawl_scrape` with tuned parameters, then write directly in Google format — token extraction is structured, not prose-only.
5. **Path C — invent.** Parses written direction, scrapes any ref URLs as mood boards, then writes an original DESIGN.md in Google format. Adds an Influences subsection citing each ref.
6. **Lint gate.** `npx @google/design.md@latest lint` runs on the staged DESIGN.md. Errors block promotion.
7. **Stage & promote.** Output staged to `/tmp/design/<slug>/`, then atomically `mv`'d into the library (never `cp`).
8. **Preview.** Path A uses catalog-authoritative previews from getdesign.md. Paths B and C generate inline self-contained HTML previews.

**Format discipline.** Library entries from before v0.3 are now in Google format — the legacy 9-section source is preserved at `<slug>/legacy/DESIGN.md` per entry. New emits target Google format from the start.

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
- **Outputs (`.arsenal/strategy/`):** `GTM_STRATEGY.md`, `REVENUE_MODEL.md`.

---

## File layout

The plugin source layout (skills, manifest):

```
arsenal-planning/
├── .claude-plugin/
│   └── plugin.json                       # plugin manifest (name: arsenal-planning)
├── skills/                               # folder names are numbered by pipeline order; frontmatter `name:` is the bare skill slug
│   ├── 01-market-analysis/
│   │   ├── SKILL.md                      # name: market-analysis
│   │   └── references/research-dispatch.md
│   ├── 02-mvp/SKILL.md                   # name: mvp
│   ├── 03-features/SKILL.md              # name: features
│   ├── 04a-ux-web/                       # 04a–04c are surface alternatives at step 4
│   │   ├── SKILL.md                      # name: ux-web
│   │   └── references/skeletons.md
│   ├── 04b-ux-app/
│   │   ├── SKILL.md                      # name: ux-app
│   │   └── references/app-patterns.md
│   ├── 04c-ux-ios/
│   │   ├── SKILL.md                      # name: ux-ios
│   │   └── references/ios-patterns.md
│   ├── 05-design/
│   │   ├── SKILL.md                      # name: design
│   │   └── references/{template,example-claude,...}.md
│   └── 06-gtm/SKILL.md                   # name: gtm
├── PIPELINE.md
├── LICENSE
└── README.md
```

`SKILL.md` is the contract — frontmatter (`name`, `description`) plus body. Claude reads `description` to decide when to auto-invoke; the body is what actually runs. Folder names carry numeric prefixes for human-readable pipeline order. Claude Code uses the frontmatter `name:` to register the skill, so slash commands stay clean (for example, `/arsenal-planning:mvp`, not `/arsenal-planning:02-mvp`).

The artifact layout that arsenal-planning writes into a *consuming project*:

```
project-root/
├── CLAUDE.md                              ← stays at root
└── .arsenal/
    ├── ARCHITECTURE.md                    ← project anchor (written by arsenal-build)
    ├── CONVENTIONS.md                     ← project anchor (written by arsenal-build)
    ├── TASKS.md                           ← project anchor (written by arsenal-build)
    ├── FEATURES.md                        ← features (single-mode)
    ├── features/                          ← features (split-mode)
    │   ├── README.md
    │   └── <slug>.md
    ├── design/                            ← grouped because design pipeline reads as a unit
    │   ├── UX.md                          ← ux-web / ux-app / ux-ios
    │   ├── DESIGN.md                      ← design
    │   ├── DESIGN_SYSTEM.md               ← written by arsenal-build
    │   └── mockups/                       ← actual mockup source or rendered files
    └── strategy/                          ← user archive; DENIED during build
        ├── research/
        │   ├── MARKET_RESEARCH.md         ← market-analysis
        │   └── RESEARCH_PLAN.md           ← market-analysis working artifact
        ├── MVP_SPEC.md                    ← mvp
        ├── GTM_STRATEGY.md                ← gtm
        └── REVENUE_MODEL.md               ← gtm
```

`~/.claude/design-md-library/<slug>/` holds the personal `design` library across projects.

## Philosophy

- **Resolve, don't catalog.** Specs end without "TBD" or "open questions." Every question gets either an answer or an explicit punt. Ambiguity is the enemy of LLM-assisted development.
- **Match depth to stakes.** A weekend side project gets a lean pass. A venture gets the deep dive. The skills read the signals and adapt.
- **Markdown only.** Planning artifacts are cheap to throw away and cheap to iterate. The expensive stuff (code, deploys) belongs to arsenal-build.

## Pairs with

- **[arsenal-build](https://github.com/Outer-Heaven-Technologies/arsenal-build)** — the execution half. Reads `.arsenal/FEATURES.md`, `.arsenal/design/UX.md`, `.arsenal/design/DESIGN.md`, and `.arsenal/design/mockups/` from this plugin's outputs and writes real code, opens PRs.

## Credits

The `design` skill emits files in the **[Google DESIGN.md format](https://github.com/google-labs-code/design.md)** (Apache 2.0) — YAML front matter for machine-readable design tokens plus an 8-section markdown body. The spec lives upstream at `google-labs-code/design.md`; arsenal-planning's `design` skill orchestrates how those files are produced and linted, but the format itself belongs to Google. Lint via `npx @google/design.md@latest lint <DESIGN.md>`.

Path A (catalog) sources known brands from [VoltAgent's getdesign.md catalog](https://github.com/VoltAgent/awesome-design-md) (MIT licensed) and transforms them into Google format — the catalog's curated brand-truth content is preserved while the file shape is normalized. Thanks to both projects' maintainers.

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
