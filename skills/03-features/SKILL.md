---
name: features
description: Drills a feature list into per-feature specs an engineer or coding agent can build without guessing — job story, user flow, Given/When/Then acceptance criteria, states, data model, dependencies. Single-file or split-file output. Use after `mvp`, or standalone when the user has a feature list and wants buildable specs before architecture decisions.
---

# Plan Features

Turn a list of feature names into specs that can actually be built. The output is either a single `.arsenal/FEATURES.md` or a split `.arsenal/features/` directory (one file per feature plus an index) — whichever fits the project size. Each spec defines a feature with enough rigor that an engineer (or `anchor-files`) can make architectural decisions without guessing.

This skill sits between `mvp` (which decides *what* to build at a high level) and `anchor-files` (which decides *how* to build it). It can also be used standalone — no prior planning docs required.

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

- **Specificity beats completeness.** A short, concrete spec is more useful than a long, vague one.
- **Drilling means resolving, not cataloging.** A finished spec has no open questions. Every question raised during drilling must be either answered (captured in the relevant section) or punted (added to the Important section as a deliberate deferral). There is no third bucket. "Open Questions" is an escape hatch that lets the spec be lazy — don't allow it.
- **The Important section is for what the agent genuinely needs.** Counter-intuitive boundaries (where an agent would build the wrong thing without an explicit "no") and critical context (architectural facts the agent must know). Most features have 2-5 items. Some have zero. If a boundary is already implicit from the User Flow or Data sections, don't restate it.
- **No timelines, no estimates.** With LLM-assisted development and variable team sizing, t-shirt sizes and week counts are noise. The spec defines *what*, not *when*.
- **The user owns the decisions.** This skill asks questions and structures answers. It doesn't invent features or override product calls.

## Communication Style

Communicate like a senior product manager in a focused 1:1 — direct, curious, low-ego, doesn't lecture. Ask one question at a time. Wait for the answer before asking the next. When the user gives a vague answer, follow up with a concrete example to choose between rather than re-explaining the question. Push back when something contradicts an earlier decision or seems out of scope. Don't summarize what the user just said back to them. Don't suggest features they didn't mention. When they make a call you disagree with, say so once and then move on.

## Output Structure

Two output structures are supported. The choice happens once at the start of Step 1 and applies for the duration of the run.

**Single-file mode:**
```
.arsenal/FEATURES.md
```
One document with one `##` section per feature.

**Split-file mode:**
```
.arsenal/features/
├── README.md                # index: name, status, priority, deps, phase
├── recipe-capture.md
├── recipe-library.md
└── ...
```
One file per feature plus an index. Each feature file contains the full spec for that feature.

**When to use which:**

| Feature count | Default suggestion |
|---|---|
| 1–3 | Single |
| 4–5 | Single (simpler, no real cost) |
| 6–8 | Split (deny-rule isolation starts paying off) |
| 9+ | Split (clearly better) |

The default is a suggestion, not a rule — the user picks. Ask once, early, with the count-aware default, then proceed.

**Why split-file mode exists:** With features in separate files, downstream skills (`arsenal-build:features` / `arsenal-build-io:features`) can read only the features relevant to the current phase, never holding the rest in context. The v0.2 gating model uses `Read(.arsenal/features/**)` as the baseline deny in `.claude/settings.json`; `arsenal-build:expand-phase` then mutates the deny list per phase to grant access to the in-scope features only (out-of-scope features stay denied by name). With one big `FEATURES.md` file, this granular per-feature gating isn't possible — deny is binary, all-on or all-off.

If a `.arsenal/strategy/MVP_SPEC.md` exists, this skill keeps it consistent with the feature docs as decisions emerge — see the "Auto-Updating MVP_SPEC.md" section below. Works the same regardless of single/split mode.

## Workflow

### Step 1: Source the Feature List

Find or build the feature list before doing anything else.

**If `.arsenal/strategy/MVP_SPEC.md` exists:** Read it. Extract the feature list from the "Must Have" and "Should Have" sections. Present the extracted list to the user and confirm: "Here's what I pulled — should we drill all of these, a subset, or do you want to add anything?"

**If no MVP_SPEC.md exists:** Ask the user for the feature list. Accept any format — bulleted, free-text, screenshot description, etc. Restate it back as a clean list and confirm before proceeding.

**Order matters.** Ask the user to confirm or change the order. The order should reflect dependencies and priority — features depended upon by others go first. Foundational/cross-cutting concerns (auth model, data architecture) should typically come first even if they're not "features" in the user-facing sense.

**Choose output structure (single vs split).** Once the feature list and order are confirmed, count the features and ask:

- 1–5 features: *"You have N features. I'll write them to a single `.arsenal/FEATURES.md`. Use split files instead (one per feature)? [single (default) / split]"*
- 6+ features: *"You have N features. Recommended: split files at `.arsenal/features/<slug>.md` plus an index — this lets `features-*` (build) read only the features relevant to a phase and supports deny-rule isolation. Use single `.arsenal/FEATURES.md` instead? [split (default) / single]"*

If user picks **split**, also confirm the directory: default `.arsenal/features/` unless project uses a different convention.

If `.arsenal/FEATURES.md` already exists from a prior run and the user is adding features, default to that structure (don't migrate without asking). If `.arsenal/features/` already exists, default to that structure. Ask only when starting fresh.

Record the chosen structure for the rest of the run. All subsequent writes follow it.

### Step 2: Choose Drilling Mode

Present three options:

- **Sequential (Option A):** Walk through one feature at a time. Ask 3–5 targeted questions per feature, write each spec, move on. Highest fidelity, slowest. Best when most features have meaningful uncertainty.
- **Draft-and-redline (Option B):** Draft all features in one pass based on context already in the conversation. User reviews and flags what's wrong. Fastest. Best when the user has already articulated most decisions in prior turns and just wants them captured.
- **Hybrid (Option C):** Draft all in one pass, then drill into the user-flagged subset. Good middle ground for medium-complexity feature sets.

Recommend a default based on context (number of features, how much detail already exists in prior turns, complexity of the domain). Let the user override.

**Default-mode heuristic:** Scan the conversation before recommending. If ≥70% of features have ≥2-3 sentences of substantive prior discussion (the user has already articulated states, edge cases, or specific behavior), default to **B (Draft-and-redline)** — most decisions are already made, just capture them. If <30% have meaningful prior discussion (the user dropped a feature list with little detail), default to **A (Sequential)** — drill each one before writing. In between, default to **C (Hybrid)**.

### Step 3: Drill Each Feature

For each feature, the goal is to fill out every section of the spec template (below) with concrete content. Use context-dependent questions — don't run a rigid questionnaire. A static "Crisis resources link" feature might need 2 questions; a "Family Controls integration" might need 15.

**Question patterns that work:**

- **Concrete-choice questions over open-ended ones.** "Should onboarding be 3 steps or 5?" beats "How should onboarding work?" Give the user something to react to.
- **Surface-the-edge-cases questions.** "What happens if the user opens this offline?" "What does the empty state look like?" "What if they tap this with no data yet?" Edge cases are where coding agents over-invent without guidance.
- **Trace-the-data questions.** "When this is created, where does it live? On-device only, or synced?" "When does it get deleted?" Forces the data model section to be real.
- **Counter-intuitive boundary questions.** "Is there anything a coding agent would naturally assume belongs in this feature that we want to forbid?" Most features have 2-3 of these. If you can't find any, the section is empty — that's fine.
- **Resolve-or-punt questions.** When a question can't be answered now, force a choice: "Decide now, or punt to the Important section as a deliberate deferral?" Never leave a question dangling for "later."

**Question patterns to avoid:**

- "What do you think about...?" (too open, invites rambling)
- "Would you like to add a feature for...?" (the skill should not invent features)
- Multi-part questions in a single turn ("How does X work, and what about Y, and have you thought about Z?")
- Questions whose answer is already in the conversation history (re-read before asking)

**When the user gives a vague answer:** Don't re-ask the same question. Offer a concrete A/B choice. "Sounds like you're between two approaches: (A) ... or (B) ... Which fits better?" Vague answers are usually a sign the user hasn't decided yet — your job is to make the decision easier, not to extract it through interrogation.

**When the user reveals a contradiction:** Flag it directly. "Earlier we said no third-party services. This feature needs an external API to work — is that a re-think, or do we need to design around it?" Don't paper over inconsistencies.

**When the user wants to add scope mid-spec:** Capture the addition, but flag whether it should be in MVP, deferred, or out of scope. Scope creep during spec-drilling is the most common failure mode of this exercise.

### Step 4: Write the Spec

After drilling each feature, write its spec using the structure defined below. Show the user the written section and ask for changes before moving on.

**Single-file mode:** Append the section to `.arsenal/FEATURES.md`. Each feature is a `##`-level heading. Add `---` separator between features.

**Split-file mode:** Write the spec to `.arsenal/features/<slug>.md`. The slug is the feature name lowercased, spaces collapsed to hyphens (e.g., "Recipe Capture" → `recipe-capture.md`, "Team Invitations" → `team-invitations.md`). The `##` heading becomes a `#` heading at the top of the file (it's now the document title, not a section). The rest of the structure is identical. After writing the file, **also update `.arsenal/features/README.md`** (the index) to reflect this feature's status, priority, and dependencies.

**Slug normalization rules** (applies to every feature name → filename conversion):
- Lowercase the wordmark
- ASCII-only — strip diacritics (e.g., "Café Listings" → `cafe-listings.md`)
- Alphanumerics preserved
- `&` → `and` before collapse (e.g., "Search & Filter" → `search-and-filter.md`)
- All other non-alphanumeric characters (spaces, `/`, `\`, punctuation, etc.) collapsed to single hyphens (e.g., "AI/ML Insights" → `ai-ml-insights.md`)
- Trim leading and trailing hyphens
- No double-hyphens (collapse runs of hyphens to one)

These rules are deterministic so the same feature name always produces the same slug across runs — important for idempotence when downstream skills reference `.arsenal/features/<exact-slug>.md` paths.

If using Option B (draft-and-redline) or Option C (hybrid), write all features first (one file per feature in split mode, or all sections in single mode), then walk through them with the user.

**Maintaining `.arsenal/features/README.md` (split mode only):**

The index is a small (≤500 token) navigation file. It contains:

```markdown
# Features Index

**Date:** [YYYY-MM-DD]
**Status:** N of M features defined

| Feature | File | Status | Priority | Depends on | Phase |
|---|---|---|---|---|---|
| Recipe Capture | [recipe-capture.md](recipe-capture.md) | Defined | Must | URL Parser | 1 |
| Recipe Library | [recipe-library.md](recipe-library.md) | Defined | Must | Recipe Capture | 1 |
| Smart Tagging | [smart-tagging.md](smart-tagging.md) | Drafting | Should | Recipe Library | 2 |
```

Update the index after every feature is written or status changes. The index is the only file in `.arsenal/features/` that downstream skills can read freely — individual feature files are read only when explicitly opted in.

### Step 5: Auto-Update MVP_SPEC.md

If `.arsenal/strategy/MVP_SPEC.md` exists, keep it consistent as decisions emerge. The feature docs are the source of truth for what's being built — MVP_SPEC reflects current state. Propagate these changes:

- **Feature deferred during drilling** → move from "Must Have" to "Won't Have" in MVP_SPEC.md with a one-line rationale.
- **Feature added during drilling** (user introduces a new feature mid-Step-3) → add it to MVP_SPEC.md under the bucket the user classified it as (Must / Should / Won't, per the "scope mid-spec" prompt in Step 3), with a one-line rationale and an `(added during feature drilling)` note so it's traceable.
- **Feature priority changes** (e.g., Must → Should after drilling reveals scope or dependencies) → move it to the new bucket in MVP_SPEC.md.
- **Feature scope changes meaningfully** → update its bullet in MVP_SPEC.md to match.
- **New dependency surfaces** (e.g., "this feature actually needs a backend") → update the architecture section of MVP_SPEC.md.
- **Data model becomes clearer** → propagate to MVP_SPEC.md's privacy/architecture section.

Tell the user when you're updating MVP_SPEC.md so they can spot-check. Format: *"Updating MVP_SPEC.md: moving X from Must Have to Won't Have because Y."* or *"Updating MVP_SPEC.md: adding Z to Should Have (added during feature drilling) because W."*

If no MVP_SPEC.md exists, skip this step entirely.

## Spec amendments during build (downstream contract)

During implementation, `arsenal-build:run-task-feature` may amend FEATURES.md / `features/*.md` in-line when build reveals reality the spec missed — a state that wasn't enumerated, an edge case in user flow, an AC threshold that needs adjustment within the same intent, a missing dependency. **Tier 1** amendments are committed alongside the code change with a `Spec amended:` commit note for auditability, and consolidated into a phase-end summary in the PR body by `arsenal-build:close-feature-phase`.

**What this means for `features`:** don't over-drill. If a State or alt-path is genuinely uncertain at plan time, leave it out — the implementer can patch it with provenance when reality surfaces it. The Important section is for counter-intuitive boundaries the agent must know; it is NOT a place to preemptively enumerate every state.

**Behavior changes** (intent shifts, not threshold adjustments) are **Tier 2**: the implementer BLOCKs with `reason: spec change required` and routes back through this skill for a fresh drill. The implementer never silently changes the feature's user-facing intent.

## Feature Spec Structure

Use this exact structure for every feature. Write in markdown. The structure is identical in single-file and split-file modes — the only difference is the heading level (`##` per feature in single mode, `#` as document title in split mode) and where the file lives.

**Single-file mode** — `.arsenal/FEATURES.md` opens with a project-level header, then each feature is a `##` section:

```markdown
# Features: [Project Name]

**Date:** [YYYY-MM-DD]
**Status:** Drilling — [N of M features defined]

---

## [Feature Name]

**Status:** Defined | Drafting | Deferred
**Priority:** Must | Should | Won't
**Depends on:** [Other feature names, or "None"]

### Purpose
One sentence. What problem this solves for the user. No marketing language.

### Job Story
When [situation], I want to [motivation], so I can [expected outcome].

(Use Job Story format from Intercom — situational, not persona-based. "When I'm 4 days into a quit attempt and an urge hits at 2am" beats "As a user, I want…")

### User Flow
Numbered steps in plain language. What the user sees, what they do, what the system does in response. Cover the happy path. Then list the most common alternate paths as sub-flows.

1. User does X
2. System shows Y
3. User chooses Z
   - Alternate path A: if user does Z', then …
   - Alternate path B: if Z is unavailable, then …

### Acceptance Criteria
Given/When/Then format. Each criterion should be testable — something you could write a test for. Be exhaustive enough that the build is unambiguous.

- **Given** [precondition], **when** [action], **then** [observable outcome]
- **Given** [precondition], **when** [action], **then** [observable outcome]

### States
List every UI state this feature can be in. Anything not listed is undefined and the implementation will guess.

- **Empty state:** [what shows when there's no data yet]
- **Loading state:** [what shows during async operations]
- **Error state:** [what shows on failure, what the recovery action is]
- **Offline state:** [behavior when network is unavailable]
- **First-run vs returning:** [how the experience differs]
- [Any other states specific to this feature]

### Data
What this feature creates, reads, updates, or deletes.

- **Entities:** [what objects exist — e.g., StreakEvent, JournalEntry]
- **Storage location:** [on-device only | synced | never persisted | server-side]
- **Schema sketch:** [shape of the data, not full DDL — fields and types]
- **Lifecycle:** [when created, when updated, when deleted, retention policy]
- **Privacy classification:** [public | private | sensitive — what would leaking this look like]

### Dependencies
- **Other features:** [features this requires]
- **Platform APIs:** [iOS / Android / web APIs needed]
- **Third-party services:** [if any — flag this clearly, especially if it conflicts with an established no-third-parties stance]
- **Permissions:** [what the user must grant — notifications, contacts, Family Controls, etc.]
- **Entitlements:** [Apple/Google entitlements that must be approved]

### Important (optional)
A short, optional list of items that are EITHER counter-intuitive boundaries (things a coding agent would reasonably assume but that we want to explicitly forbid) OR critical context that the agent needs to do the work correctly. Most features have 2-5 items. Some have zero. Skip the section entirely if there is nothing genuinely important to flag.

What belongs here:
- **Counter-intuitive boundaries:** "Do not branch quiz questions based on prior answers — all 18 are asked of every user." (An agent would naturally invite adaptive branching; the boundary prevents over-engineering.)
- **Critical architectural context:** "All data lives in the user's CloudKit. This feature stores nothing on the server."
- **Non-obvious deferrals that have data model implications:** "Edit history is not retained at MVP — each edit overwrites. Phase 2 will store history; design data model with that future in mind."

What does NOT belong here:
- Anything already implicit from the User Flow, Data, or other sections (don't restate "no voice input" if the User Flow already says "free-text field")
- Defensive overclaiming (don't write "no content moderation" or "no tutorial" unless an agent would reasonably build one)
- Wishlist items framed as negatives (don't write "no AI suggestions" unless the agent would have a reason to add them)

Test for inclusion: "Would a competent coding agent benefit from seeing this, OR would they likely build the wrong thing without it?" If neither, omit it.

---

## [Next Feature]

[Same structure...]
```

**Split-file mode** — each feature lives in its own file `.arsenal/features/<slug>.md`. The same structure applies, but the feature heading becomes the document title at `#` level (since it's the only feature in the file):

```markdown
# [Feature Name]

**Status:** Defined | Drafting | Deferred
**Priority:** Must | Should | Won't
**Depends on:** [Other feature names with file paths, or "None"]

[... rest of structure identical to single-file mode ...]
```

The project-level header (date, drilling status) lives in `.arsenal/features/README.md` instead of being repeated per file.

## Worked Example

Here is what a well-drilled feature looks like. Use this as a quality bar — every feature spec (whether in `FEATURES.md` or `.arsenal/features/<slug>.md`) should be at least this concrete.

```markdown
## Recipe Capture

**Status:** Defined
**Priority:** Must
**Depends on:** URL Parser, Local Storage

### Purpose
Let the user save a recipe from any cooking website by pasting a URL, with ingredients, steps, and source attribution captured automatically.

### Job Story
When I find a recipe I want to try later, I want to capture it from any site without copying and pasting fields, so I can build a personal cookbook without leaving the page I'm reading.

### User Flow
1. User opens the app and taps "Add Recipe."
2. Paste-URL field appears with the keyboard focused.
3. User pastes a URL and taps "Capture."
4. App fetches the page, runs the parser, and shows a preview screen with title, ingredients, steps, and image.
5. User edits any field inline if the parser got something wrong.
6. User taps "Save" — recipe lands in their library, tagged with the source domain.
7. If the parser fails entirely, the user is shown the raw page text in an editable field as a fallback.

### Acceptance Criteria
- **Given** the user pastes a valid recipe URL, **when** they tap "Capture," **then** the app fetches and parses the page within 5s and shows a populated preview.
- **Given** the parser succeeds, **when** the preview appears, **then** every field (title, ingredients, steps, image) is editable inline.
- **Given** the parser fails, **when** the preview would otherwise appear, **then** the raw page text is shown in a single editable field with a "save anyway" option.
- **Given** the user has already saved this URL, **when** they tap "Capture" on it again, **then** the existing recipe opens (not duplicated) with a banner: "You saved this on [date]."
- **Given** the user is offline, **when** they tap "Capture," **then** the URL is queued and parsed automatically when connectivity returns.

### States
- **Empty state (first run):** "No recipes yet. Paste a URL above to start your cookbook."
- **Loading state:** Spinner over the paste field while the parser runs. Cancellable.
- **Error state — fetch fail:** "Couldn't reach that page. Check the URL or try again." Retry button.
- **Error state — parse fail:** Raw text fallback as above.
- **Duplicate state:** Existing recipe opens (not re-parsed); banner shown.

### Data
- **Entities:** `Recipe`, `Ingredient`, `Step`
- **Storage location:** Local-first (on-device DB). No cloud sync at MVP.
- **Schema sketch:**
  - `Recipe`: `id: UUID`, `title: String`, `sourceURL: URL`, `imageURL: URL?`, `capturedAt: Date`
  - `Ingredient`: `id: UUID`, `recipeID: UUID`, `name: String`, `quantity: String?`, `position: Int`
  - `Step`: `id: UUID`, `recipeID: UUID`, `text: String`, `position: Int`
- **Lifecycle:** Recipe created on Save tap. Updated when the user edits inline. Deleted only via explicit delete in library. Never auto-pruned.
- **Privacy classification:** Standard — recipes are user-curated content the user has chosen to save.

### Dependencies
- **Other features:** URL Parser (foundational), Recipe Library (where saved recipes appear)
- **Platform APIs:** Network/HTTP for fetch
- **Third-party services:** None — parser uses schema.org/Recipe JSON-LD as primary, heuristics as fallback
- **Permissions:** None

### Important
- Do not auto-categorize recipes by cuisine/diet/tags at MVP. The parser sees enough to fill ingredients and steps, but cuisine inference is wrong often enough that wrong tags create distrust. Users add tags manually if they want.
- Do not pre-fetch recipes the user has only viewed but not saved. Saving is the explicit user intent — pre-fetching invades privacy and wastes bandwidth.
- The parser supports schema.org/Recipe JSON-LD as the primary path and falls back to heuristics for sites without it. Do not build a per-site adapter library — that's a maintenance pit.
- Single user only at MVP. Do not build infrastructure for shared cookbooks or multi-user libraries. Single device, single library.
```

## Quality Bar

Before considering the feature specs complete (whether in `FEATURES.md` or `.arsenal/features/`), every feature should pass these checks:

- **Could a coding agent build this without asking questions?** If they'd need to make assumptions, the spec is incomplete.
- **Are there any unresolved questions?** There shouldn't be. Every question raised during drilling should be answered (in the relevant section) or punted (to Important as a deliberate deferral). No "TBD," no "decide later."
- **Are all the states listed?** Empty, loading, error, offline, first-run vs returning. Don't let the implementation guess.
- **If the Important section exists, does every item earn its place?** Each item should either be a counter-intuitive boundary (where the agent would build the wrong thing without it) or critical context (architectural fact the agent must know). Items that restate the obvious should be removed.
- **Is every dependency on another feature listed?** Implicit dependencies cause integration bugs.
- **Is the data lifecycle clear?** When created, when updated, when deleted, where stored, what happens on logout/reinstall.

## Adaptive Depth

Match the depth of drilling to the feature's complexity and risk:

| Feature type | Depth | Approximate question count |
|---|---|---|
| Static content (links, legal pages, static copy) | Light | 1–3 questions |
| Standard UI feature (settings toggle, list view, simple form) | Moderate | 3–6 questions |
| Multi-state interactive feature (onboarding, multi-step flow) | Deep | 8–15 questions |
| Platform-API integration (Family Controls, Health, Notifications) | Deep + research | 10–20 questions, may need to consult docs |
| Cross-cutting architecture (auth model, sync strategy) | Deep + research | 15+ questions, treat as foundational |

If a feature is genuinely simple, don't pad the spec with fake complexity. A 4-line spec for a "Crisis resources link" is fine — it's just a menu item with three URLs.

## Important Guidelines

- **Re-read the conversation before asking each question.** Don't ask things the user already answered earlier.
- **Don't invent features.** If the user's list has 8 features, the output has 8 features. The skill structures, doesn't expand.
- **Push back once, then move on.** If the user makes a call you disagree with, flag it once with the concrete reason. If they hold the position, write the spec their way and don't bring it up again.
- **Capture decisions, not arguments.** "Alternatives Considered" lists what was rejected and why — not the back-and-forth that got there.
- **No code in feature specs.** Schema sketches use plain field-and-type notation, not Swift/Kotlin/SQL. Implementation language belongs in `anchor-files`.
- **No design specs in feature specs.** "Largest element on screen" is fine. Hex colors, font sizes, exact spacing belong in the design phase.
- **Stop when done with the right handoff for the project type.** When all features are drilled and the user confirms the docs are complete, end with a handoff that routes by project type. Don't generically point at `anchor-files` — most projects need UX and design planning first.
  - **UI projects (marketing site, authenticated webapp, native iOS, etc.):** next is the `ux-*` variant that matches the surface (`ux-web` for marketing sites, `ux-app` for authenticated webapps, `ux-ios` for native iOS) → then `design` → then `anchor-files`. Example: *"`.arsenal/FEATURES.md` is ready. Recommended next: `ux-app` to map screens and engagement model, then `design` for the visual spec, then `anchor-files` to scaffold the foundation."*
  - **Non-UI projects (CLI, library, API, server-only):** skip `ux-*` and `design`. Next is `anchor-files` directly. Example: *"`.arsenal/FEATURES.md` is ready. Recommended next: `anchor-files` to scaffold the technical foundation."*
  - In split mode, substitute `.arsenal/features/` (N feature files + README index) for `.arsenal/FEATURES.md` in either handoff.
  - **Pick the variant based on intake.** Step 1 already established what's being built; don't re-ask the user. If you're unsure between `ux-web` and `ux-app`, ask once before producing the handoff.
- **Cap output files at ~500 lines.** Long markdown bloats downstream context and becomes unscannable. Sweet spot: 100–400 lines. In single mode, if `FEATURES.md` would exceed 500 lines, switch to split mode (one file per feature). In split mode, if any individual feature file would exceed 500 lines, split it into a parent + sub-files (e.g., `auth.md` + `auth-flows.md` + `auth-edge-cases.md`) and cross-link.
