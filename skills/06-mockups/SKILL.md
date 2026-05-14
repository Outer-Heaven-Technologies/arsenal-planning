---
name: mockups
description: Generates two-pass anchor-strategy mockup briefs from upstream FEATURES, UX, and DESIGN — copy-paste-ready prompts engineered for Claude Design, Stitch, Open Design, or v0. Hard-requires upstream planning (FEATURES, UX, DESIGN) to exist; refuses and routes to the right arsenal-planning skill when artifacts are missing. Briefs save to `.arsenal/strategy/mockup-briefs/` for manual handoff to mockup tools today and future automation via generate-mockups. Use whenever you need mockups before running arsenal-build:setup and design-*. Triggers include "generate mockup briefs", "prepare mockups", "draft mockup prompts", "script mockups", "set up mockup generation", "I need mockups", "write mockup prompts".
---

# Plan Mockups

Generate well-formed mockup briefs that produce production-grade outputs from AI mockup tools. The skill applies a **two-pass anchor strategy**: lock the visual language with one or two anchor screens first, then derive every other screen against the locked anchor. Briefs are tool-agnostic by default — they work for Claude Design, Stitch, Open Design, and v0 — with tool-specific tweaks available.

This skill writes briefs in `.arsenal/strategy/mockup-briefs/`. It does **not** generate mockups itself. The user feeds each brief into their tool of choice and saves the output to `.arsenal/design/mockups/`. A future `generate-mockups` skill (planned around `nexu-io/open-design`) will consume these same briefs automatically.

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

## Role

- **Bridge** between planning (`design`) and the manual mockup-generation step.
- **Brief writer**, not mockup writer. Briefs are prompts; mockups are outputs.
- **Two-pass anchor strategy enforcer.** Pass 1 locks the visual language. Pass 2 inherits it. Pass 3 (where needed) renders state variants.

## Why the anchor strategy

Most teams generate inconsistent mockups by going straight to per-feature screens. The mockup tool has to re-derive the visual language each time, producing screens that don't feel like one product. The anchor strategy fixes this by separating the "lock the look" decision from the "place this screen's content" decision.

| Pass | Goal | What the tool produces | Iterations expected |
|---|---|---|---|
| **1 — Anchor** | Establish the visual language | One mockup that locks: typography hierarchy, spacing rhythm, color application, component visual weight, navigation shape | 5-15 |
| **2 — Derived** | Place per-screen content into the locked language | Per-screen mockups inheriting Pass 1's grammar | 1-3 per screen |
| **3 — States** | Variants for empty / loading / error / first-run | State variants per high-stakes screen | 1 per state |

Pass 1 is the highest-leverage prompt in the project. It deserves heavy iteration. Pass 2 is mostly mechanical once Pass 1 is right. Pass 3 is near-mechanical.

## References

**Read `references/worked-examples.md` when authoring a brief.** It contains:

- **Tool quickstart matrix** — input format, output format, sweet spot, and key gotcha for Claude Design, Stitch, Open Design, and v0
- **Pass 1 worked examples** for the same screen across all three primary tools (Claude Design / Stitch / Open Design), with tool-specific prompt formulations side-by-side
- **Pass 2 worked example** with brief notes on per-tool variation
- **Pass 3 worked example** (state variant)
- **Common iteration patterns** — concrete redirects when the first output is too generic, drifts from the anchor, introduces colors not in the brand palette, generates charts when you wanted dots, etc.
- **Quick reference for web-marketing and iOS Pass 1** — the canonical example uses a web-app, but the file shows how the brief shape adjusts for other surfaces

Reading the worked examples is the difference between writing a "fillable template" brief and writing a brief whose `## The prompt` block reads like the prompts that actually produced good output for someone else. Don't author Pass 1 from scratch without consulting it.

## Preconditions (hard-required)

| Artifact | Required | If missing |
|---|---|---|
| `.arsenal/FEATURES.md` or `.arsenal/features/` | **Yes** | Stop. *"Run `features` first — `mockups` needs per-feature User Flow, States, and Important sections to write concrete briefs."* |
| `.arsenal/design/UX.md` | **Yes** | Stop. *"Run `ux-{web,app,ios}` first — `mockups` needs the screen inventory and component names."* |
| `.arsenal/design/DESIGN.md` | **Yes** | Stop. *"Run `design` first — `mockups` needs the brand spec to anchor visual references."* |

No fallback. Mockup quality is bounded by upstream artifact quality; this skill refuses to paper over missing planning.

## What gets generated

`.arsenal/strategy/mockup-briefs/` directory:

```
.arsenal/strategy/mockup-briefs/
├── README.md                                # index + workflow + status tracker
├── 01-anchor-<screen>.md                    # Pass 1 (1-2 briefs per project)
├── 02-<screen>.md                           # Pass 2 derived screens
├── 02-<another-screen>.md
├── ...
├── 03-<screen>-<state>.md                   # Pass 3 state variants (where needed)
└── ...
```

## Workflow

### Step 1: Verify preconditions

Hard-check upstream artifacts. On any miss, stop with the routing message above. No discovery interview; no fallback.

```bash
ls .arsenal/FEATURES.md .arsenal/features/README.md .arsenal/design/UX.md .arsenal/design/DESIGN.md 2>/dev/null
```

### Step 2: Classify surface and select anchor(s)

Determine project surface from upstream artifacts (which `ux-*` variant was upstream is the strongest signal):

| Surface | Anchor screen(s) | Rationale |
|---|---|---|
| Web marketing (`ux-web`) | Homepage | Locks brand application, hero pattern, nav, footer, section grammar, button hierarchy. Most other pages inherit from it. |
| Web app (`ux-app`) | Dashboard / today view | Locks sidebar/top-nav, card style, type scale, spacing system, color use. App shell decisions cascade through every screen. |
| Native iOS (`ux-ios`) | **Home/today + onboarding step 1** (two anchors) | iOS has two distinct visual languages — in-app and onboarding. Onboarding is often more cinematic. Lock both before deriving. |
| Hybrid (multiple UX docs) | One anchor per surface | Each surface gets its own anchor; downstream briefs reference whichever anchor applies. |

For the rest of this workflow, "anchor screen" means whichever screen(s) Step 2 identified.

### Step 3: Generate Pass 1 anchor brief(s)

For each anchor screen, write a brief at `.arsenal/strategy/mockup-briefs/01-anchor-<screen>.md`. Use the Brief Format below.

Pass 1 input pulls:
- Full `.arsenal/design/DESIGN.md` (the entire brand spec — this is where DESIGN.md pays off most)
- `.arsenal/design/UX.md § <Anchor Section>` only (one section)
- **No feature content.** Placeholder content prompts the tool to focus on look, not specifics.

The Pass 1 brief's `## The prompt` block should explicitly tell the tool: *"This is the anchor mockup that will establish the visual vocabulary for the rest of the product — every other screen will reference it. Don't worry about specific content; use placeholder data. Focus on typography hierarchy, spacing rhythm, color application, component visual weight, and navigation shape."*

### Step 4: User confirmation gate (Pass 1)

After writing Pass 1 brief(s), **stop**. Tell the user:

> *"Generated Pass 1 anchor brief at `.arsenal/strategy/mockup-briefs/01-anchor-<screen>.md`. Feed this into Claude Design / Stitch / Open Design / v0. Iterate until the visual language is locked. When you have a final anchor mockup, save it to `.arsenal/design/mockups/anchor-<screen>.{png,jsx,html}` and tell me to continue. I'll then generate Pass 2 briefs that reference the locked anchor."*

Do not generate Pass 2 briefs until the user confirms Pass 1 is locked. Pass 2 briefs need the actual anchor mockup as visual reference; generating them prematurely produces briefs that re-derive the language instead of inheriting it.

### Step 5: Generate Pass 2 briefs (derived screens)

Once the user confirms Pass 1 is locked:

1. Verify `.arsenal/design/mockups/anchor-<screen>.{png,jsx,html}` exists.
2. Identify high-stakes screens from `.arsenal/design/UX.md` (every page/screen with its own section that isn't an anchor).
3. For each, write a brief at `.arsenal/strategy/mockup-briefs/02-<screen>.md`.

Pass 2 input pulls per screen:
- Reference to the locked anchor mockup (path + brief description of what was locked)
- `.arsenal/design/UX.md § <Screen Section>` (one section)
- Relevant feature spec(s)' **visual sections only**: User Flow, States, Important, Data. Skip Acceptance Criteria (GWT) and Dependencies — those are test/architecture specs, not visual specs, and waste tool context.
- Cross-references to `DESIGN.md` by section. Do not restate tokens.

The Pass 2 brief's `## The prompt` block should explicitly tell the tool: *"The visual language is already locked — reference [anchor mockup]. Match it exactly. Just place this screen's specific content into that visual grammar."*

### Step 6: User confirmation gate (Pass 2 → 3)

After writing Pass 2 briefs, tell the user:

> *"Generated N Pass 2 briefs in `.arsenal/strategy/mockup-briefs/`. Feed each into your tool, save the resulting mockups to `.arsenal/design/mockups/<screen>.{png,jsx,html}`. If any screens have meaningful state coverage (empty / loading / error / first-run from FEATURES § States) and you want variant mockups, tell me to continue and I'll generate Pass 3 state variants."*

Pass 3 is optional. Many projects don't need it (especially marketing sites). Wait for the user to confirm need.

### Step 7: Generate Pass 3 briefs (state variants — when needed)

For each screen flagged with meaningful states in FEATURES § States, write a state variant brief at `.arsenal/strategy/mockup-briefs/03-<screen>-<state>.md`.

Pass 3 input pulls per state:
- Reference to the Pass 2 mockup for the screen (visual reference)
- FEATURES § States entry for the specific state (state copy, behavior)
- Direction: *"Generate the [state] variant of this screen. Reference: [Pass 2 mockup]. State copy: [from FEATURES]. Visual treatment: subdued/distinct, but inherit the screen's grammar."*

### Step 8: Write index and final report

Write `.arsenal/strategy/mockup-briefs/README.md`:

```markdown
# Mockup Briefs Index

**Surface:** [web-marketing | web-app | ios | hybrid]
**Generated:** [date]
**Anchor(s):** [list]

## Workflow order

1. **Pass 1** — run anchor brief(s) first. Iterate heavily. Save locked mockup(s) to `.arsenal/design/mockups/anchor-*.{png,jsx,html}`.
2. **Pass 2** — once anchor is locked, run derived-screen briefs. Each references the anchor.
3. **Pass 3 (optional)** — state variants for screens that need them.

## Briefs

| Brief | Pass | Screen | Status | Output |
|---|---|---|---|---|
| `01-anchor-<screen>.md` | 1 | <screen> | draft / ready / used | `.arsenal/design/mockups/anchor-<screen>.{ext}` |
| `02-<screen>.md` | 2 | <screen> | draft / ready / used | `.arsenal/design/mockups/<screen>.{ext}` |
| ... | ... | ... | ... | ... |

## Recommended tools

[Per the surface, recommend the closest-fit tool. See tool-specific guidance in the skill.]
```

Final report to the user:
- Briefs generated (count + paths)
- Recommended tool based on surface (see Tool-specific guidance below)
- Next action: feed Pass 1 brief into the chosen tool, iterate, save the anchor mockup, return.

## Brief format

Every brief follows this structure. Keep tight — briefs are prompts, not docs.

```markdown
---
pass: 1 | 2 | 3
screen: <slug>
surface: web-marketing | web-app | ios
status: draft | ready | used
state: (Pass 3 only — empty | loading | error | first-run)
output_path: .arsenal/design/mockups/<screen>.{png,jsx,html}
visual_reference: (Pass 2+ only — path to the locked anchor or Pass 2 mockup)
---

# Pass <N> Brief — <Screen Name>

## Surface and frame
- **Artifact type:** [landing page | dashboard | mobile screen | onboarding step | etc.]
- **Platform & frame:** [desktop | mobile-first | iPad | iPhone 15 Pro | etc.]
- **Output target:** `.arsenal/design/mockups/<screen>.{png,jsx,html}`

## Audience and job-to-be-done
- **Audience:** [one sentence — who's looking at this screen]
- **JTBD:** [one sentence — what they're trying to accomplish on this screen]

## Tone
[Adjective ladder, 2-4 terms — e.g., "editorial > playful > corporate" or "calm > minimal > warm"]

## Brand tokens (cross-reference, don't restate)
- See `.arsenal/design/DESIGN.md` for the full brand spec.
- Key emphasis for this screen: [§ Color Palette, § Typography, § Spacing — whichever sections matter most]

## Sections / hierarchy (in display order)
1. **[Section name]** — [one-line content rationale]
2. **[Section name]** — [rationale]
3. ...

## States to render
- **Primary (this brief):** [main state — populated / typical / happy-path]
- [Pass 3 brief: state-specific copy from FEATURES § States]

## Visual reference
- [Pass 1: none — establish the visual language using DESIGN.md tokens]
- [Pass 2: `.arsenal/design/mockups/anchor-<screen>.{png,jsx,html}` — match this visual language exactly]
- [Pass 3: `.arsenal/design/mockups/<screen>.{png,jsx,html}` — variant of this screen]

## Do's and Don'ts
- **Do:** [explicit positives — e.g., "use serif headlines for hero copy"]
- **Don't:** [explicit negatives — e.g., "no stock photos", "no gradients on text", "no tab badges as marketing"]

## The prompt

> [Copy-paste-ready prompt block. Combines the above into a single paragraph the user pastes into their tool. Tool-agnostic by default; reads well in Claude Design / Stitch / Open Design / v0.]

## Notes

[Iteration guidance. Related briefs. What to watch for. Empty if nothing notable.]
```

## Tool-specific guidance

The brief format above is portable, but each tool has a sweet spot. Note these in the brief's `## Notes` section or in the README's recommendation block.

### Claude Design (Anthropic Labs)

- **Best for:** Pass 2 and Pass 3 — derived screens and state variants. Four refinement modes (chat, inline comments on specific elements, direct edits, custom sliders per tunable property) make iteration cheap.
- **Lean into:** the **visual reference** field. Claude Design has multimodal input — paste the anchor mockup as an image and the tool inherits the language naturally. Web-capture tool grabs reference elements from live sites.
- **Iteration mode:** use **inline comments** for surgical fixes ("this card needs more padding"). Use **custom sliders** for tone-of-voice tweaks (spacing, color saturation, type weight).
- **Output:** interactive prototypes, mockups, wireframes, slides. Good when you want clickable flow rather than static screens. ZIP export is importable into Open Design.

### Stitch (Google Labs, Gemini 2.5)

- **Best for:** mobile-first iOS and responsive web screens. The "Zoom-Out-Zoom-In" pattern fits naturally with the anchor strategy.
- **Lean into:** the **DESIGN.md cross-reference**. Stitch re-passes the full DESIGN.md every turn — keeping the prompt short and pointing at DESIGN.md (rather than restating tokens inline) gives Gemini consistent context every iteration.
- **Iteration mode:** **one screen or component at a time.** Don't ask for multi-screen sweeping changes — Gemini degrades.
- **Output:** responsive UI screens + front-end code (HTML/React). Good fit when you want `.jsx` or `.html` straight out.
- **Special note:** Stitch accepts a DESIGN.md file directly. Attach `.arsenal/design/DESIGN.md` to the conversation, then the brief reads as a focused per-screen prompt.

### Open Design (nexu-io)

- **Best for:** structured artifacts with strong DESIGN.md grounding. The discovery form locks the surface decisions that Pass 1 is meant to settle.
- **Lean into:** the brief structure that answers Open Design's discovery form upfront (surface, audience, tone, brand context, scale, constraints). The Brief Format above is already shaped for this — Open Design will accept it cleanly.
- **Skill selection:** Open Design has 31 built-in skills (saas-landing, dashboard, mobile-app, magazine-poster, social-carousel, deck, pm-spec, etc.). Name the closest match in the brief's `## Notes` so the user picks the right skill during dispatch.
- **Iteration mode:** multi-turn with five-dimensional self-critique (Philosophy / Hierarchy / Detail / Function / Innovation). The tool will critique its own output — useful for catching slop.

### v0 (Vercel)

- **Best for:** React/Next projects where you want JSX you can drop into the codebase. Component-level mockups especially.
- **Lean into:** explicit framework version (React 19, Next 15, Tailwind 4, shadcn/ui 2.x). v0 generates against current versions.
- **Iteration mode:** chat-based refinement plus visual-edit mode.
- **Output:** JSX/TSX with Tailwind classes. The output drops directly into `.arsenal/design/mockups/<screen>.jsx`.

## Universal prompt patterns (all tools)

These hold across every mockup tool. The brief format encodes them — keeping them visible here so the skill keeps generating briefs that follow them.

1. **Ground tokens, not adjectives.** "Minimalist blue" loses; `#0F62FE / Inter / 8px base / 12px radius` wins. DESIGN.md cross-reference does this work.
2. **Lock the surface before the pixels.** Pass 1 exists for this reason. Don't skip it.
3. **One artifact per turn.** Tools degrade when asked to produce many screens or many decisions in one shot. Pass 2 = one brief per screen.
4. **Specify what NOT to do.** Every brief includes a Don'ts section. Explicit negatives prevent generic output.
5. **Iteration is cheaper than perfection.** Briefs are first drafts of prompts; the user iterates the prompt + the output together.
6. **Visual reference beats verbal reference.** Pass 2+ always cites the anchor mockup. Without visual reference, the tool re-derives the language.

## Anti-patterns

- **Don't dump all briefs at once.** Generate Pass 1 → wait → Pass 2 → wait → Pass 3. Each pass needs the previous pass's output as visual reference.
- **Don't restate DESIGN.md content in briefs.** Cross-reference precise sections. The tool either has DESIGN.md attached (Stitch, Open Design) or can reference it (Claude Design with codebase access, v0 with context).
- **Don't include Acceptance Criteria (GWT) in mockup briefs.** They're test specs, not visual specs. Mockup tools waste context interpreting them.
- **Don't generate Pass 3 for every screen.** Only screens with meaningful state coverage in FEATURES § States. Most screens have one or two states that matter; the rest is mechanical.
- **Don't write briefs longer than ~200 lines.** Briefs are prompts. Long briefs split tool attention and waste context budget.
- **Don't skip the visual-reference field on Pass 2+.** Without it, the tool re-derives the visual language and Pass 1's work is wasted.

## File layout

```
project-root/
└── .arsenal/
    └── strategy/
        └── mockup-briefs/
            ├── README.md                              # index + workflow + status
            ├── 01-anchor-dashboard.md                 # Pass 1
            ├── 01-anchor-onboarding-step-1.md         # Pass 1 (iOS second anchor)
            ├── 02-morning-ritual.md                   # Pass 2
            ├── 02-evening-reflection.md
            ├── 02-habit-detail.md
            ├── 02-settings.md
            ├── 03-dashboard-empty.md                  # Pass 3
            ├── 03-ritual-completion.md
            └── ...
```

## Important guidelines

- **Briefs are prompts, not docs.** Keep them tight and actionable. ~100-200 lines per brief.
- **The anchor is the highest-leverage prompt.** Iterate it heavily. Don't move to Pass 2 until Pass 1 is locked.
- **One screen per brief.** Don't combine screens, even related ones (e.g., onboarding step 1 and step 2 each get their own brief).
- **Status tracking matters.** Update the brief's frontmatter `status` field as the user moves through (draft → ready → used). The future `generate-mockups` skill will use this to know what to dispatch.
- **The brief's `## The prompt` block is what the user copy-pastes.** Everything above it is context for you (the skill) when authoring; everything below is metadata. The prompt block is what matters at runtime.
