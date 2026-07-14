# Pipeline — arsenal-planning

The planning half of the arsenal pipeline. Skills chain from idea → MVP spec → feature specs → UX → design → GTM. Each skill writes a markdown artifact the next reads, so you never answer the same question twice. You can enter at any stage — skills detect upstream artifacts and skip questions they already have answers to.

Downstream from planning is **execution**, which lives in [arsenal-build](https://github.com/Outer-Heaven-Technologies/arsenal-build). The artifacts produced here (`MARKET_RESEARCH.md`, `MVP_SPEC.md`, `FEATURES.md`, `UX.md`, `DESIGN.md`, `GTM_STRATEGY.md`, `REVENUE_MODEL.md`) are at canonical paths under `.arsenal/` for any execution system to consume. Actual mockups, when available, live in `.arsenal/design/mockups/`.

## Linear flow

```
   [market-analysis ──►] mvp ──► features ──► ux-* ──► design ──► (handoff to arsenal-build) ──► gtm
        │                  │           │           │          │                                      │
        ▼                  ▼           ▼           ▼          ▼                                      ▼
   MARKET_RES…        MVP_SPEC.md  FEATURES.md  UX.md     DESIGN.md                           GTM_STRATEGY.md
   (unified              (focused      or         (UX)       (brand)                            REVENUE_MODEL.md
    dossier;             spec)        features/*.md
    optional)
```

Actual mockup source or rendered files may be added directly to `.arsenal/design/mockups/`. The build orchestrator inspects them with the canonical planning documents and expands implementation work in the single `.arsenal/TASKS.md` ledger.

**`market-analysis` is optional upstream of `mvp`.** Run it for serious projects (startups, investor decks, market-entry decisions); skip for prototypes / hobby projects / client work. `mvp` reads `MARKET_RESEARCH.md` when present and falls back to lightweight inline research when not.

**`gtm` runs after the MVP is built (or nearly built).** It pulls from `MVP_SPEC.md` and `MARKET_RESEARCH.md` to produce launch positioning, channels, pricing, and revenue modeling.

## ux-* surface routing

Pick the right variant based on what you're building:

| You're building | Use | Why |
|---|---|---|
| Marketing site / landing page / agency portfolio / waitlist | `ux-web` | Conversion-driven, public-facing, measured in signups/leads/purchases |
| SaaS app surface / internal tool / dashboard / productivity webapp | `ux-app` | Engagement-driven, behind auth, repeat use; cares about app shell, empty states, Cmd-K |
| Native iOS app (any kind) | `ux-ios` | Onboarding flow, paywall compliance, permission timing, HIG conventions |
| SaaS with marketing + app | Both `ux-web` and `ux-app` | Run sequentially; each writes to its own `UX.md` section or use separate files |
| iOS app with marketing site | Both `ux-web` and `ux-ios` | Run sequentially |

All three write to `.arsenal/design/UX.md`. For hybrid products, run two of them sequentially.

## Artifact dependency graph

Which file each skill **reads** (`R`) and **writes** (`W`):

| Skill | Reads | Writes |
|---|---|---|
| `market-analysis` | — | `.arsenal/strategy/research/MARKET_RESEARCH.md` (unified executive dossier — market + customer + industry structure + Porter's all 5 + conditional PESTLE + SWOT + risks + recommendations), `.arsenal/strategy/research/RESEARCH_PLAN.md` (working artifact, retained as historical record) |
| `mvp` | `.arsenal/strategy/research/MARKET_RESEARCH.md` (optional) | `.arsenal/strategy/MVP_SPEC.md` |
| `features` | `MVP_SPEC.md` (optional) | `.arsenal/FEATURES.md` (single) **or** `.arsenal/features/<slug>.md` + `.arsenal/features/README.md` (split) |
| `ux-web` / `ux-app` / `ux-ios` | `MVP_SPEC.md`, `ARCHITECTURE.md`, `CLAUDE.md` (all optional) | `.arsenal/design/UX.md` |
| `design` | — | `.arsenal/design/DESIGN.md` + `~/.claude/design-md-library/<slug>/` |
| `gtm` | `MVP_SPEC.md`, `MARKET_RESEARCH.md`, `ARCHITECTURE.md`, `CONVENTIONS.md` | `.arsenal/strategy/GTM_STRATEGY.md`, `.arsenal/strategy/REVENUE_MODEL.md` |

`*` = required. All others soft — skill prompts to run upstream or proceeds with reduced fidelity.

## Cross-plugin handoff

Once planning is complete, downstream execution is handled by [arsenal-build](https://github.com/Outer-Heaven-Technologies/arsenal-build). The handoff is filesystem-based — arsenal-build reads canonical paths.

| Planning artifact | Downstream consumer (arsenal-build) |
|---|---|
| `.arsenal/strategy/MVP_SPEC.md` | Optional product context; protected during build execution |
| `.arsenal/FEATURES.md` / `.arsenal/features/*.md` | Build orchestrator, scoped to the active phase |
| `.arsenal/design/UX.md` | Build orchestrator (required for UI projects) |
| `.arsenal/design/DESIGN.md` | Build orchestrator (required for UI projects) |
| `.arsenal/design/mockups/` | Build orchestrator (optional actual mockup evidence) |
| `.arsenal/strategy/research/MARKET_RESEARCH.md` | GTM and other strategy work |

**Recommended handoff point:** after FEATURES, UX, and DESIGN are complete and any actual mockups have been placed in `.arsenal/design/mockups/`, invoke arsenal-build. Its orchestrator consolidates architecture and conventions, then plans implementation in the single `.arsenal/TASKS.md` ledger.

If you don't have arsenal-build installed, planning artifacts remain at canonical paths and can be consumed by any equivalent execution system.

## Entry-point matrix

Where to start given what you already have:

| You have | Start with | Skip |
|---|---|---|
| Just an idea, need full validation | `market-analysis` → `mvp` | — |
| Just an idea, want spec only (skip executive research) | `mvp` (does lightweight inline research) | `market-analysis` |
| Need research dossier for investor deck / market scouting | `market-analysis` standalone | everything else |
| External validation, no spec | `features` | `market-analysis`, `mvp` |
| `MVP_SPEC.md` only | `features` (or hand off to arsenal-build directly for small/non-UI) | `market-analysis`, `mvp` |
| `FEATURES.md` + want UX | `ux-web` / `ux-app` / `ux-ios` (pick by surface) | `mvp`, `features` |
| Working product, no launch | `gtm` | everything |

When you enter mid-pipeline, the active skill detects upstream artifacts and uses them as context. Missing artifacts trigger prompts: hard prompts when the artifact is high-leverage; soft notes otherwise.

## Branching paths

Skip stages that don't fit the project:

| Stage | Skip when | Cost of skipping |
|---|---|---|
| `market-analysis` | Hobby project, client work, idea already validated externally | No SWOT / structural-forces analysis; `mvp` falls back to lightweight inline research |
| `mvp` | Spec already written; idea well-defined externally | None for the spec, but no go/pivot/kill analysis |
| `features` | <4 features and they're simple (static pages, basic forms) | Downstream build pipelines fall back to UX-driven task expansion — less concrete |
| `ux-*` | Non-UI projects (CLI, library, API, infra) | Build phases become pure feature-domain work with no design pass |
| `design` | Existing brand spec; no UI; CLI/server-only | No canonical brand reference for the build pipeline |
| `gtm` | Hobby project; no launch goals; internal tool | None |

## Depth adaptation

Most skills adapt depth to the project's stakes:

| Signal | Adaptation | Skills that adapt |
|---|---|---|
| "side project", "weekend build", "hobby" | Lean (or **Brief** format for `mvp`) | `mvp` (Brief), `gtm` (Lean), `features` |
| "freelance", "client work", "small business" | Moderate (or **Standard** format) | `mvp` (Standard), `gtm` (Moderate) |
| "startup", "raising money", "$X MRR target" | Deep (or **Comprehensive** format) | `mvp` (Comprehensive), `gtm` (Deep — full revenue model) |

Pass intent signals during the first message of any skill to lock the adaptation early. Skills will announce which tier they picked and let you override.

**Note on `mvp` specifically:** intent signals drive its output format, while research depth depends on available context. It uses an existing `MARKET_RESEARCH.md` dossier when present and otherwise offers lightweight inline grounding or a handoff to `market-analysis` for deeper research.

## Auto-trigger vs explicit invocation

These require explicit invocation (no auto-trigger via natural language):

- `mvp` — produces a full planning suite; opting in is deliberate
- `features` — produces multiple feature specs; opting in is deliberate
- `gtm` — produces a launch plan; deliberate

Auto-fire on natural language matching their description:

- `ux-web` — fires on "scaffold the landing page", "what sections does this homepage need", marketing/site phrasing
- `ux-app` — fires on "plan the app UX", "scaffold the dashboard", "what screens does this app need"
- `ux-ios` — fires on "plan the iOS UX", "scaffold the iPhone app", "design the onboarding flow", "plan the paywall"
- `market-analysis` — fires on "research the market for X", "do a competitive analysis for Y", "validate this market", "build me a market dossier", "executive research on Z", "should I enter this space"
- `design` — fires on "get me Shopify", "make a DESIGN.md like <Brand>", "extract design from <url>", "inspired by X", "design that feels like X meets Y"

If a skill that should auto-fire is misfiring (or not firing when it should), the description in `SKILL.md` is the lever.

## File layout (planning artifacts)

```
project-root/
├── CLAUDE.md                              ← stays at root
└── .arsenal/
    ├── ARCHITECTURE.md                    ← project anchor (arsenal-build)
    ├── CONVENTIONS.md                     ← project anchor (arsenal-build)
    ├── TASKS.md                           ← project anchor (arsenal-build)
    ├── FEATURES.md                        ← features (single mode)
    ├── features/                          ← features (split mode — alternative)
    │   ├── README.md                      ← index
    │   ├── feature-a.md
    │   └── feature-b.md
    ├── design/                            ← grouped because design pipeline reads as a unit
    │   ├── UX.md                          ← ux-web / ux-app / ux-ios
    │   ├── DESIGN.md                      ← design (do-not-edit)
    │   ├── DESIGN_SYSTEM.md               ← written by arsenal-build
    │   ├── ux/                            ← optional split — per-page/screen sub-files
    │   │   ├── home.md
    │   │   └── onboarding.md
    │   └── mockups/                       ← actual mockup source or rendered files
    └── strategy/                          ← user archive; DENIED during build
        ├── research/
        │   ├── MARKET_RESEARCH.md         ← market-analysis unified dossier
        │   └── RESEARCH_PLAN.md           ← working artifact, retained
        ├── MVP_SPEC.md                    ← mvp
        ├── GTM_STRATEGY.md                ← gtm
        └── REVENUE_MODEL.md               ← gtm
```

`~/.claude/design-md-library/<slug>/` holds the personal `design` library across projects.

Artifact paths and file names are fixed by the canonical `.arsenal/` contract.
