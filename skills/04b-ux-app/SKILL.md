---
name: ux-app
description: >
  Generate `UX.md` defining the UX architecture for an authenticated web application — a tool the user opens repeatedly to do work, not a marketing site. Covers app shell (sidebar, header, command palette), screen inventory, navigation hierarchy, empty states, and the engagement model that drives retention. Use whenever the user is planning a SaaS app surface, internal tool, productivity app, personal dashboard, daily-driver tool, or any web app behind auth designed for repeat use. Triggers include "plan the app UX," "scaffold the dashboard," "what screens does this app need," "lay out the app shell," "design the IA for this tool," "plan the productivity app structure." Do NOT use for marketing websites (use `ux-web`) or native iOS apps (use `ux-ios`). For hybrid products, run this skill for the app surface and `ux-web` separately for any marketing pages.
---

# Plan UX (App)

Generate a `UX.md` file: the UX backbone of an authenticated web application. Web apps are different from marketing sites — the user is already in, they're not converting, they're *doing their work*. The skeleton defines the app shell, screen inventory, navigation patterns, engagement loops, and empty states that make the tool feel native and stay used.

**Use this skill for:**
- SaaS product app surfaces (the part behind login)
- Internal tools and dashboards
- Productivity apps (task managers, note-takers, habit trackers — web versions)
- Personal dashboards and meaning-layer tools
- Admin panels
- Daily-driver tools designed for repeat ritual use
- Data-heavy applications

**Do NOT use this skill for:**
- Marketing sites or landing pages → use `ux-web`
- Native iOS apps → use `ux-ios`
- Hybrid products → use this skill for the app, run `ux-web` separately for any marketing surface

**UX vs UI separation:**
- `UX.md` = UX — app architecture, screen inventory, navigation hierarchy, engagement loops, empty states, what goes where and why
- `DESIGN_SYSTEM.md` = UI — how it looks (tokens, components, motion)

Generate `UX.md` first. `DESIGN_SYSTEM.md` references it. If `.arsenal/design/DESIGN_SYSTEM.md` already exists from arsenal-build, cross-check after generation.

## Paths

All arsenal artifacts live under `.arsenal/` at the project root.

| What | Path | Notes |
|---|---|---|
| Strategy archive (denied during build) | `.arsenal/strategy/` | MVP_SPEC.md, GTM_STRATEGY.md, REVENUE_MODEL.md, research/{MARKET_RESEARCH,RESEARCH_PLAN}.md |
| Feature specs | `.arsenal/FEATURES.md` (single-mode) or `.arsenal/features/<slug>.md` (split-mode) | Build reads the specs cited by the active phase |
| Project anchor docs | `.arsenal/{ARCHITECTURE,CONVENTIONS,TASKS}.md` | Always readable during build |
| Design reference set | `.arsenal/design/{UX,DESIGN,DESIGN_SYSTEM}.md` + `.arsenal/design/mockups/` | Always readable during build |

**Build-time access:** `.arsenal/strategy/` is protected during build. The orchestrator reads only active feature specs, design references, and the single `.arsenal/TASKS.md` ledger; host-specific permission rules may enforce this.

## Workflow

### Step 1: Read existing planning context

Before asking the user anything, look for and read these files if they exist:

- `.arsenal/strategy/MVP_SPEC.md`
- `.arsenal/ARCHITECTURE.md`
- `CLAUDE.md`
- Any other `.arsenal/strategy/*` documents

Extract what's already known: product description, target user, entity model, feature set, design constraints. Web apps are typically defined by their *data model and the workflows over that data* — the planning docs should make both clear.

### Step 2: Classify the app shape

Web apps come in distinct shapes, and the shape determines the screen inventory. Classify the project before generating screens:

| Shape | Description | Examples | Primary engagement |
|---|---|---|---|
| **Productivity tool** | Helps user manage their work — tasks, notes, calendars | Things 3, Sunsama, Linear | Daily-driver workflow |
| **Daily-driver / ritual app** | Designed for repeat structured use, often with reflection | Ledger, Stoic, Journey | Ritual completion |
| **Dashboard / analytics** | Surfaces data the user makes decisions from | Mixpanel, PostHog, Grafana | Periodic deep-dives |
| **Content creation tool** | User produces content — design, docs, code | Figma, Notion, VS Code | Sustained focus sessions |
| **Communication tool** | Real-time or async messaging | Slack, Linear comments, Front | Continuous low-attention |
| **Internal admin tool** | Operate the business — CRUD, ops dashboards | Retool builds, internal Rails apps | Task completion |
| **Marketplace / catalog** | Browse + transact within an authenticated context | Notion templates, Figma community | Discovery then commit |

A product may blend two shapes (e.g., a daily-driver tool with a built-in metrics view is daily-driver + dashboard hybrid). Identify the primary and any secondary.

### Step 3: Determine the engagement model

Marketing sites convert; web apps engage. The engagement model is what drives retention:

- **Ritual model** — User opens the app at predictable moments (morning, end of day) and follows a structured flow. Success = ritual completion rate.
- **Workflow model** — User opens the app to complete specific tasks throughout the day. Success = task completion time, throughput.
- **Reference model** — User opens the app to find information or check state. Success = time to answer, accuracy.
- **Creation model** — User opens the app to build something. Success = artifacts produced, session length.
- **Monitoring model** — User opens the app to check on something. Success = anomaly detection, decision speed.

A web app often has multiple models active across different screens (a daily-driver tool might use the Ritual model for its morning and evening check-in flows, the Reference model on its dashboard, and the Workflow model on its primary work surface). Identify per screen.

### Step 4: Define the app shell

Web apps have a persistent shell — the chrome that surrounds every screen. The shell decisions cascade through everything else:

**Sidebar vs. top nav:**
- **Sidebar** — best for apps with 4+ primary destinations or deep nested navigation. Allows collapse. Works well at scale. Default for most productivity apps and dashboards.
- **Top nav** — best for apps with 3-5 primary destinations and shallow hierarchy. More marketing-feel. Works for content apps.
- **Hybrid** — top nav for primary destinations, sidebar for contextual navigation within a section.

**Persistent banner / header strip:**
- Useful when there's *always-relevant context* the user benefits from seeing (a goal/intent banner, current-cycle indicator, status or sync indicators).
- Should not exceed ~40px and should be visually subtle.

**Command palette (Cmd-K):**
- Strongly recommended for any web app with more than ~10 screens or actions. Enables keyboard-first power users.
- Should support: jump-to-screen, quick-create entities, quick-search, recent items.

**Quick-create / global actions:**
- A floating button or keyboard shortcut to create the most common entity from anywhere.

**Decide explicitly:** sidebar shape, what lives in the persistent header (if anything), Cmd-K behavior, global quick actions.

### Step 5: Generate the screen inventory

For each shape, common screens. Customize aggressively for the actual product.

**Productivity tool / daily-driver baseline:**

- **Home / dashboard** — the orienting view, shown after login or as the default
- **List/board view** — primary data view (kanban, list, tree)
- **Detail view / panel** — opens for a single entity (task, note, goal)
- **Quick capture** — frictionless entity creation, often modal or palette
- **Settings** — preferences, integrations, account
- **Onboarding** — first-run experience for new users
- **Search results** — global search across the app

**Dashboard / analytics baseline:**

- **Dashboard** — main view with metrics and visualizations
- **Drill-down detail** — for clicking into a metric
- **Filter / segment builder** — defining the data slice
- **Saved views** — bookmarked filter combinations
- **Settings** — data source connections, alerting, account

**Content creation baseline:**

- **Library / index** — list of artifacts the user has created
- **Editor / canvas** — the creation surface itself
- **Detail panel** — metadata about the active artifact
- **Settings** — preferences, integrations

For each screen, decide whether it's a full route, a sheet/modal, a sidebar panel, or an inline overlay. Modern web apps blend these heavily.

### Step 6: Define empty states per screen

Empty states are where most web apps fail. A user arrives at a list view with no data and sees a blank screen — they bounce. Every screen with user-generated data needs a designed empty state:

**Empty state patterns:**

- **Educational empty state** — explain what this screen will show once there's data, with a primary CTA to create the first item
- **Sample data empty state** — pre-populate with example data clearly labeled as "Sample" so the user can see what the screen looks like populated. Used by analytics tools.
- **Single-CTA empty state** — one clear next action, minimal explanation. Used when the action is obvious.
- **Onboarding empty state** — multi-step guided creation of first item, used during initial setup.

For each list/board view in the screen inventory, specify which empty state pattern applies and what the empty-state copy/CTA should be.

### Step 7: Per high-stakes screen, define

For each main screen, document:

- **Screen role** — what is this screen for? (orient / capture / triage / reflect / configure / etc.)
- **Engagement model** — which model from Step 3 applies here
- **Primary action** — the single most important action available on this screen
- **Sections in display order** — what the user sees top-to-bottom, with rationale
- **Components needed** — named components for `DESIGN_SYSTEM.md`
- **Empty state** — what happens when there's no data
- **Anti-patterns** — what NOT to do for this screen type
- **Cross-references** — links to `ARCHITECTURE.md` (data flows) and `DESIGN_SYSTEM.md` (components)

**Output format per screen:**

```markdown
## [Screen name]

**Screen role:** [orient | capture | triage | reflect | edit | configure | etc.]
**Engagement model:** [ritual | workflow | reference | creation | monitoring]
**Primary action:** [the single most important thing the user does here]

### Sections (in display order)
1. **[Section name]** — [one-line rationale]
2. **[Section name]** — [rationale]
...

### Components needed
- [Component name] — [brief description]
- ...

### Empty state
- **Pattern:** [educational | sample-data | single-CTA | onboarding]
- **Copy direction:** [what the empty state communicates]
- **CTA:** [the primary action available in empty state]

### Anti-patterns
- [What not to do] — [why]
- ...

### Cross-references
- ARCHITECTURE.md → [relevant entity, data flow, query pattern]
- DESIGN_SYSTEM.md → [relevant components]
```

### Step 8: Look up engagement-model patterns

Open `references/app-patterns.md` and review patterns for the dominant engagement model and shape. Apply the listed best practices and anti-patterns. The reference covers:

- App shell patterns (sidebar, top nav, command palette, banner)
- Engagement model patterns (ritual flow, workflow board, dashboard, editor)
- Common screen patterns (dashboard, list view, detail view, settings, onboarding, empty states)
- Anti-patterns by shape (productivity tool, dashboard, daily-driver, etc.)
- Industry skeleton examples (productivity tool baseline)

### Step 9: Review & cross-check

After generating, tell the user:

- Where the file was created
- How many screens were specified (full detail vs one-line)
- The app shell decisions made (sidebar/top-nav, banner, Cmd-K)
- If `.arsenal/design/DESIGN_SYSTEM.md` exists: list components named in `UX.md` that don't have entries, and offer to add them or suggest running `design`
- If `.arsenal/ARCHITECTURE.md` exists: cross-check that screens reference real entities from the data model
- Offer to flesh out any screen that needs more detail

## File placement

```
project-root/
└── .arsenal/
    └── design/
        └── UX.md
```

If no `.arsenal/design/` directory exists, ask the user where to put it.

## Important guidelines

- **Engagement, not conversion.** Don't import marketing-page thinking. The user is already in. No hero, no social proof, no pricing. Focus on the work they're trying to do.
- **App shell decisions cascade.** Decide sidebar/top-nav, persistent banner, command palette, and global quick-create *first*. Every screen sits inside those decisions.
- **Empty states are first-class.** Every list/board view needs a designed empty state. The blank-screen-on-first-use is the #1 retention killer.
- **Engagement model per screen.** Different screens within one app can have different models. A daily-driver app might have ritual screens, reference screens, and configuration screens — each shaped by its model.
- **Components are the bridge to DESIGN_SYSTEM.md.** Name them specifically: "Habit row" not "row." "Goal status pill" not "badge."
- **Anti-patterns are the highest-signal part.** Web apps have specific failure modes: Kanban with no statuses, dashboard that opens to blank, sidebar with 30 items, settings buried 3 clicks deep. Include 3-5 per screen.
- **Cmd-K and keyboard-first matter.** Any non-trivial web app benefits. Don't ship a productivity tool without it.
- **Mobile considerations.** Web apps are often used on mobile (PWA). For each screen, note: is this usable on a phone? What collapses, what changes? Don't assume desktop-only.
- **Cap `UX.md` at ~500 lines.** If it would exceed, split into a parent `UX.md` (screen inventory + shell decisions + global anti-patterns) plus per-screen sub-files in a `ux/` folder (e.g., `ux/dashboard.md`, `ux/settings.md`) and cross-link.
- **Don't over-specify.** The skeleton names sections and components. The actual visual design happens in `DESIGN_SYSTEM.md` and frontend code. `UX.md` is the blueprint, not the build.
