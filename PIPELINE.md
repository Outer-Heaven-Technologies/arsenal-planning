# Pipeline — arsenal-planning

The planning half of the arsenal pipeline. Skills chain from idea → MVP spec → feature specs → UX → design → mockup briefs → GTM. Each skill writes a markdown artifact the next reads, so you never answer the same question twice. You can enter at any stage — skills detect upstream artifacts and skip questions they already have answers to.

Downstream from planning is **execution**, which lives in [arsenal-build](https://github.com/Outer-Heaven-Technologies/arsenal-build). The artifacts produced here (`MARKET_RESEARCH.md`, `MVP_SPEC.md`, `FEATURES.md`, `UX.md`, `DESIGN.md`, `mockup-briefs/`, `GTM_STRATEGY.md`, `REVENUE_MODEL.md`) are at canonical paths under `.arsenal/` for any execution system to consume.

## Linear flow

```
   [market-analysis ──►] mvp ──► features ──► ux-* ──► design ──► mockups ──► [user generates mockups] ──► (handoff to arsenal-build) ──► gtm
        │                  │           │           │          │            │                                                                  │
        ▼                  ▼           ▼           ▼          ▼            ▼                                                                  ▼
   MARKET_RES…        MVP_SPEC.md  FEATURES.md  UX.md     DESIGN.md   .arsenal/strategy/                                                GTM_STRATEGY.md
   (unified              (focused      or         (UX)       (brand)      mockup-briefs/*.md                                              REVENUE_MODEL.md
    dossier;             spec)        features/*.md
    optional)
```

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
| `mockups` | `FEATURES.md` / `features/`*, `.arsenal/design/UX.md`*, `.arsenal/design/DESIGN.md`*; reads `.arsenal/design/mockups/anchor-*.{png,jsx,html}` once Pass 1 is locked | `.arsenal/strategy/mockup-briefs/01-anchor-*.md` (Pass 1), `02-*.md` (Pass 2), `03-*-<state>.md` (Pass 3, optional), `README.md` (index) |
| `gtm` | `MVP_SPEC.md`, `MARKET_RESEARCH.md`, `ARCHITECTURE.md`, `CONVENTIONS.md` | `.arsenal/strategy/GTM_STRATEGY.md`, `.arsenal/strategy/REVENUE_MODEL.md` |
| `dispatch-parallel` (utility, off-pipeline) | 2–5 investigation descriptions (CLI args or `--from-file`); args: `--investigation`, `--from-file`, `--surface`, `--force`, `--max` | `.arsenal/tasks/parallel/<run-id>/investigation-N-result.md` per investigation (≤3k tokens), `SUMMARY.md` (aggregated, with file-overlap detection and severity-tagged recommendations) |

`*` = required. All others soft — skill prompts to run upstream or proceeds with reduced fidelity.

## Cross-plugin handoff

Once planning is complete, downstream execution is handled by [arsenal-build](https://github.com/Outer-Heaven-Technologies/arsenal-build). The handoff is filesystem-based — arsenal-build reads canonical paths.

| Planning artifact | Downstream consumer (arsenal-build) |
|---|---|
| `.arsenal/strategy/MVP_SPEC.md` | `setup` (optional context), `landing` |
| `.arsenal/FEATURES.md` / `.arsenal/features/*.md` | `setup` (required), `generate-feature-briefs`, `generate-design-briefs` |
| `.arsenal/design/UX.md` | `setup` (required for UI projects), `generate-design-briefs` |
| `.arsenal/design/DESIGN.md` | `setup` (required for UI projects) |
| `.arsenal/strategy/mockup-briefs/` | User feeds into Claude Design / Stitch / Open Design / v0; outputs land in `.arsenal/design/mockups/`, consumed by `arsenal-build:design` |
| `.arsenal/strategy/research/MARKET_RESEARCH.md` | `landing` (competitive analysis), `gtm` |

**Recommended handoff point:** after `mockups` writes briefs and the user has generated mockups into `.arsenal/design/mockups/`, run `arsenal-build:setup` to consolidate planning into the agent reference layer (CLAUDE.md, `.arsenal/ARCHITECTURE.md`, `.arsenal/CONVENTIONS.md`, `.arsenal/design/DESIGN_SYSTEM.md`, `.arsenal/TASKS.md`).

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
| `FEATURES.md` + `UX.md` + `DESIGN.md`, no mockups | `mockups` | `mvp`, `features`, `ux-*`, `design` |
| Working product, no launch | `gtm` | everything |

When you enter mid-pipeline, the active skill detects upstream artifacts and uses them as context. Missing artifacts trigger prompts: hard prompts when the artifact is high-leverage; soft notes otherwise.

## Branching paths

Skip stages that don't fit the project:

| Stage | Skip when | Cost of skipping |
|---|---|---|
| `market-analysis` | Hobby project, client work, idea already validated externally | No SWOT / structural-forces analysis; `mvp` falls back to lightweight inline research |
| `mvp` | Spec already written; idea well-defined externally | None for the spec, but no go/pivot/kill analysis |
| `features` | <4 features and they're simple (static pages, basic forms) | Downstream build pipelines fall back to UX-driven task expansion — less concrete |
| `ux-*` | Non-UI projects (CLI, library, API, infra) | Downstream `setup` skips `DESIGN_SYSTEM.md`; phases become pure feature-domain (no design half) |
| `design` | Existing brand spec; no UI; CLI/server-only | No canonical brand reference for the build pipeline |
| `mockups` | Non-UI projects; mockups already produced manually or imported | Design pipeline runs without concrete visual reference — visual fidelity output is less consistent |
| `gtm` | Hobby project; no launch goals; internal tool | None |

## Depth adaptation

Most skills adapt depth to the project's stakes:

| Signal | Adaptation | Skills that adapt |
|---|---|---|
| "side project", "weekend build", "hobby" | Lean (or **Brief** format for `mvp`) | `mvp` (Brief), `gtm` (Lean), `features` |
| "freelance", "client work", "small business" | Moderate (or **Standard** format) | `mvp` (Standard), `gtm` (Moderate) |
| "startup", "raising money", "$X MRR target" | Deep (or **Comprehensive** format) | `mvp` (Comprehensive), `gtm` (Deep — full revenue model) |

Pass intent signals during the first message of any skill to lock the adaptation early. Skills will announce which tier they picked and let you override.

**Note on `mvp` specifically:** the signals above drive `mvp`'s *output format* (Brief 3-5pp / Standard 5-10pp / Comprehensive 10-15pp body), not research effort. `mvp` always runs deep research via `dispatch-parallel` regardless of tier — the format scales the dossier verbosity, not the research methodology. The format is chosen at Step 4d (after research is complete) with an intent-based recommendation; the user can override.

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
- `mockups` — fires on "generate mockup briefs", "prepare mockups", "draft mockup prompts", "script mockups", "set up mockup generation", "write mockup prompts", "I need mockups"
- `dispatch-parallel` — fires on "investigate these in parallel", "fan out on these issues", "run these checks concurrently", "audit X, Y, and Z separately"

If a skill that should auto-fire is misfiring (or not firing when it should), the description in `SKILL.md` is the lever.

## File layout (planning artifacts)

```
project-root/
├── CLAUDE.md                              ← stays at root
└── .arsenal/
    ├── config.yaml                        ← optional path override
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
    │   └── mockups/                       ← user-generated from mockups briefs via Claude Design / Stitch / Open Design / v0
    ├── tasks/                             ← per-task briefs + ephemera (gitignored)
    │   ├── phase-N/
    │   ├── parallel/<run-id>/             ← dispatch-parallel working dir
    │   │   ├── investigation-N-result.md
    │   │   └── SUMMARY.md
    │   └── archive/
    └── strategy/                          ← user archive; DENIED during build
        ├── MARKET_RESEARCH.md             ← market-analysis (unified executive dossier)
        ├── RESEARCH_PLAN.md               ← market-analysis (working artifact, retained)
        ├── MVP_SPEC.md                    ← mvp
        ├── mockup-briefs/                 ← mockups (two-pass anchor briefs)
        │   ├── README.md                  ← index + workflow + status tracker
        │   ├── 01-anchor-<screen>.md      ← Pass 1 (1-2 briefs per project)
        │   ├── 02-<screen>.md             ← Pass 2 derived screens
        │   └── 03-<screen>-<state>.md     ← Pass 3 state variants (optional)
        ├── GTM_STRATEGY.md                ← gtm
        └── REVENUE_MODEL.md               ← gtm
```

`~/.claude/design-md-library/<slug>/` holds the personal `design` library across projects.

**File names are not configurable.** The artifact contract depends on canonical names; only the wrapping `.arsenal/` directory can be reconfigured via `.arsenal/config.yaml` if absolutely needed (rare).
