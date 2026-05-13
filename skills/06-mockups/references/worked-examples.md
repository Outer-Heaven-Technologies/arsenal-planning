# Worked Examples — mockups

**Last updated:** 2026-05  
**Purpose:** Concrete worked examples for each pass of the two-pass anchor strategy, shown across the four supported mockup tools (Claude Design, Stitch, Open Design, v0). Read when authoring a brief and you want to see a real prompt shape rather than just the template.

---

## Tool quickstart matrix

| Tool | Input format | Output format | Sweet spot | Key gotcha |
|---|---|---|---|---|
| **Claude Design** (Anthropic Labs) | Multimodal — text + image upload + doc upload (DOCX/PPTX/XLSX) + codebase + web-capture | Interactive prototypes, mockups, wireframes, slides; ZIP export | Pass 2/3 — derived screens and state variants, since iteration is cheap (chat, inline comments, direct edits, custom per-property sliders) | Vague prose without role/constraints wastes its multimodal strength. Lean into visual reference and web-capture. |
| **Stitch** (Google Labs, Gemini 2.5) | Text + image inputs + **DESIGN.md attached** (9-section format, Apache 2.0 spec) | Responsive UI screens + front-end code (HTML / React) | Mobile-first iOS, one-screen-at-a-time refinement, "Zoom-Out-Zoom-In" pattern | Don't restate DESIGN.md tokens in the prompt — Stitch re-passes the full DESIGN.md every turn. Keep prompts short and reference DESIGN.md sections. |
| **Open Design** (nexu-io, BYOK) | Structured discovery form (surface, audience, tone, brand, scale, constraints) + DESIGN.md + skill selection (31 built-in) + project metadata | Sandboxed HTML in iframe; exports to HTML / PDF / PPTX / ZIP / Markdown; pixel-accurate device frames | Structured single-artifact surfaces; multi-turn with five-dimensional self-critique (Philosophy / Hierarchy / Detail / Function / Innovation) | Skill + DESIGN.md selection is doing half the work. Pick the right skill from the catalog and attach DESIGN.md before the brief. |
| **v0** (Vercel) | Text prompt + image upload + codebase context | JSX / TSX with Tailwind classes, drop-in to Next.js / React projects | Component-level mockups for React/Next codebases; iterative refinement via chat + visual-edit mode | Specify framework + library versions explicitly (React 19, Next 15, Tailwind 4, shadcn/ui 2.x). v0 generates against current versions. |

**Docs links:**
- Claude Design: [anthropic.com/news/claude-design-anthropic-labs](https://www.anthropic.com/news/claude-design-anthropic-labs)
- Stitch: [stitch.withgoogle.com/docs/design-md/format](https://stitch.withgoogle.com/docs/design-md/format/)
- Open Design: [github.com/nexu-io/open-design](https://github.com/nexu-io/open-design)
- v0: [v0.app](https://v0.app)

---

## Example project (used in all worked examples)

To keep the examples concrete, all three passes below use a single fictional project: **Streakly**, a calm web habit-tracker for solo developers and creatives.

The upstream artifacts (sketched — these aren't real files, just what they'd contain for this example):

**`planning/MVP_SPEC.md` (sketched):**
- Target user: solo developers / creatives / professionals building 1-3 daily habits
- Core value loop: open the app → see today → check in → feel quiet pride → return tomorrow
- Anti-positioning: not gamified; no XP bars, levels, badges, or progress percentages

**`planning/features/habit-capture.md` (sketched):**
- Job story: When I want to track a new behavior, I want to capture it in under 10 seconds, so I don't talk myself out of starting.
- States: empty (first run), creating (modal), active, paused
- Important: do not auto-suggest habits; the user names them

**`planning/features/daily-checkin.md` (sketched):**
- Job story: When I open the app each morning, I want to see what's due and check in on yesterday, so I stay honest without effort.
- States: untouched (today not started), in-progress (some habits checked), complete

**`docs/UX.md` (sketched):**
- Pages: Dashboard (today), Habit Detail, Weekly Review, Settings, Paywall, Onboarding
- App shell: minimal top bar (wordmark + settings); no sidebar; floating quick-capture button
- Empty states: every list view has a designed empty state

**`docs/DESIGN.md` (sketched):**
- Atmosphere: calm, warm, minimal, editorial
- Color: sand canvas `#F5F1EC`, charcoal text `#1F1B16`, single olive accent `#7A8C5E`, error rust `#B05050`
- Typography: serif headlines (Souvenir Pro 28/24/20), sans-serif body (Inter 16/14)
- Spacing: 8px base; 12px corner radius
- Voice: gentle nudge, no gamification

---

## Pass 1 worked examples — Dashboard anchor

The Pass 1 brief locks the visual language. Same screen, same project, three tool-specific prompt formulations.

### Pass 1 brief (canonical template, before tool-specific tweaks)

```markdown
---
pass: 1
screen: dashboard
surface: web-app
status: ready
output_path: docs/mockups/anchor-dashboard.png
---

# Pass 1 Brief — Streakly Dashboard (Anchor)

## Surface and frame
- **Artifact type:** dashboard (single screen)
- **Platform & frame:** web, desktop-first (mobile responsive)
- **Output target:** `docs/mockups/anchor-dashboard.png`

## Audience and job-to-be-done
- **Audience:** solo developers, creatives, professionals tracking 1–3 daily habits
- **JTBD:** open the app in the morning, see what to do today, check in on yesterday

## Tone
calm > warm > editorial > minimal

## Brand tokens (cross-reference, don't restate)
See `docs/DESIGN.md` for the full spec.  
Key emphasis: § Color Palette (sand canvas, olive accent, no third color), § Typography (serif headlines / sans body), § Spacing (8px base rhythm)

## Sections / hierarchy (in display order)
1. **Top bar** — wordmark left, settings icon right; no nav menu
2. **Greeting strip** — "Good morning, [name]" + today's date, serif display
3. **Today's habits** — 3 habit cards stacked vertically; each shows habit name, current streak count, and a quick-check-in button
4. **Streak overview** — horizontal strip showing 3–4 active streaks with gentle visual indicator
5. **Quick capture** — floating action button bottom-right to add a new habit

## States to render
- **Primary:** populated — user has 3 habits, all with active streaks

## Visual reference
None — this is the anchor. Establish the visual language using DESIGN.md tokens.

## Do's and Don'ts
- **Do:** lead with serif personality in the greeting strip — the typography is the brand moment.
- **Do:** use the olive accent sparingly — one element drawing attention per screen.
- **Don't:** no gamification mechanics (XP, levels, badges, progress percentages) — these conflict with the calm voice the brand is built on.
- **Don't:** no stock photos, no illustrations, no decorative imagery beyond the wordmark.
- **Don't:** no charts or data visualizations.

## The prompt
[See per-tool variations below — the same context above turns into three different prompt shapes]

## Notes
- Iterate on the typography hierarchy and spacing rhythm in this brief — those cascade through every other screen.
- The greeting strip is the highest-leverage typography moment. Get the serif feeling right here first.
```

### Pass 1 — Claude Design prompt

**Setup:** attach `docs/DESIGN.md` to the conversation. Optionally use web-capture to grab one reference site that captures the desired feel (e.g., a publication or editorial product whose typography you admire).

```
Design the dashboard screen for Streakly, a calm web habit-tracker for solo
developers and creatives. Desktop-first, mobile responsive.

I've attached docs/DESIGN.md with the full brand spec — sand canvas #F5F1EC,
charcoal text #1F1B16, single olive accent #7A8C5E, Souvenir Pro serif
headlines, Inter body, 8px spacing rhythm, 12px corner radius.

The screen has five sections top to bottom:
1. Top bar with wordmark and settings icon (no nav menu)
2. Serif greeting strip ("Good morning, Sarah" + today's date)
3. Three habit cards stacked vertically (habit name, current streak count,
   quick-check-in button)
4. Horizontal streak overview strip
5. Floating action button bottom-right for quick capture

This is the anchor mockup — focus on locking typography hierarchy, spacing
rhythm, color application, and the calm/warm/editorial feel. Every other
screen in the product will inherit this visual vocabulary. Use placeholder
content.

Tone: calm > warm > editorial > minimal. Lead with serif personality in the
greeting. Use the olive accent for one element per screen, no more. Do not
introduce gamification mechanics (XP bars, levels, badges, progress
percentages) — those conflict with the brand's quiet pride voice.
```

**Iteration moves:** drop **inline comments** on specific cards ("this card needs more breathing room — tighten the spacing"); use **custom sliders** for tone tweaks (e.g., generate a "serif weight" slider to dial in the headline feel).

### Pass 1 — Stitch prompt

**Setup:** attach `docs/DESIGN.md` (Stitch consumes the 9-section format directly and re-passes it every turn). Keep the prompt short — Stitch handles brand context via DESIGN.md, not via prose.

```
Design the dashboard for Streakly — calm web habit-tracker, desktop-first,
mobile responsive. Reference the attached DESIGN.md for all brand tokens
(sand canvas, charcoal text, single olive accent, Souvenir Pro serif
headlines, Inter body, 8px spacing).

Five sections top to bottom:
1. Top bar (wordmark left, settings icon right)
2. Serif greeting strip with today's date
3. Three habit cards (name, streak count, quick-check-in)
4. Horizontal streak overview
5. Floating quick-capture button bottom-right

Tone: calm, warm, editorial, minimal. Placeholder content. This is the
anchor screen — focus on typography hierarchy and the calm/editorial feel.
No gamification (XP, levels, badges, percentages). Lead with serif in the
greeting.
```

**Why shorter:** Stitch's DESIGN.md re-pass means brand context is always loaded. Restating tokens in the prompt wastes context and can cause Gemini to over-weight prompt tokens over DESIGN.md tokens.

**Iteration moves:** one screen or component at a time. Don't ask for "the dashboard and also the habit detail" in one prompt — Stitch degrades on multi-screen sweeping changes.

### Pass 1 — Open Design prompt

**Setup:** select the `dashboard` skill from Open Design's catalog (or `webapp-product` if `dashboard` doesn't fit perfectly). Attach `docs/DESIGN.md`. The discovery form will surface the surface/audience/tone questions — answer them inline before the free-text prompt.

```
Discovery form answers:
- Surface: dashboard (authenticated web app)
- Audience: solo developers / creatives building 1–3 daily habits
- Scale: solo product, single-user surface
- Brand context: attached DESIGN.md (Streakly — calm warm editorial)
- Tone: calm > warm > editorial > minimal
- Constraints:
  - No gamification (XP, levels, badges, progress percentages)
  - No stock photos, illustrations, or decorative imagery
  - No charts or data viz
  - Single accent color (olive) used once per screen max
  - Lead with serif personality in headline moments

Free-text brief:
Design the Streakly dashboard — five sections top to bottom:
1. Top bar (wordmark left, settings icon right)
2. Serif greeting strip with today's date
3. Three habit cards stacked vertically (name, streak count, check-in button)
4. Horizontal streak overview strip
5. Floating quick-capture button bottom-right

Placeholder content. This is the anchor mockup — every other screen inherits
this visual vocabulary. Focus the iteration on typography hierarchy and the
spacing rhythm.

Use the dashboard skill. Run the five-dimensional critique pass after the
first generation.
```

**Why this shape:** Open Design's discovery form does heavy lifting on surface/audience/tone. The free-text prompt only needs to cover sections and intent. The five-dimensional self-critique catches inconsistencies before you do.

---

## Pass 2 worked example — Habit Detail (derived screen)

Pass 2 inherits Pass 1's locked visual language. The brief references the anchor mockup as visual reference; the prompt tells the tool to match it exactly.

### Pass 2 brief

```markdown
---
pass: 2
screen: habit-detail
surface: web-app
status: ready
output_path: docs/mockups/habit-detail.png
visual_reference: docs/mockups/anchor-dashboard.png
---

# Pass 2 Brief — Habit Detail

## Surface and frame
- **Artifact type:** detail screen
- **Platform & frame:** web, desktop-first (mobile responsive)
- **Output target:** `docs/mockups/habit-detail.png`

## Audience and job-to-be-done
- **Audience:** same as anchor — solo dev/creative with active habits
- **JTBD:** review a specific habit's history, see streak detail, edit or pause it

## Tone
Same as anchor — calm > warm > editorial > minimal

## Brand tokens (cross-reference, don't restate)
See `docs/DESIGN.md`. Inherit visual language from `docs/mockups/anchor-dashboard.png`.

## Sections / hierarchy (in display order)
1. **Top bar** — back-arrow left, edit/pause icon right
2. **Habit header** — habit name in serif display, current streak count as a small badge
3. **30-day history strip** — gentle dots, olive-filled for completed days, sand for missed
4. **Stats summary** — longest streak / completion rate / total check-ins (small grid)
5. **Recent reflections** — last 3 daily check-in notes if any (small cards)
6. **Action row** — primary "Check in for today" button

## States to render
- **Primary:** 14-day current streak with 2 missed days in the last 30 days

## Visual reference
`docs/mockups/anchor-dashboard.png` — match the visual language exactly: typography hierarchy, spacing rhythm, color application, card style.

## Do's and Don'ts
- **Do:** reuse the same card style as the dashboard's habit cards.
- **Do:** use serif for the habit name — typography moment matters here too.
- **Don't:** don't introduce any new color values beyond what the anchor used.
- **Don't:** no charts or graphs — the dot strip is the only data viz.

## The prompt

> Design the habit-detail screen for Streakly. The visual language is already
> locked — reference [paste anchor mockup or attach image]. Match it exactly:
> typography hierarchy, spacing rhythm, color application, card style.
>
> This screen shows one habit's detail. Sections top to bottom:
> 1. Top bar (back-arrow left, edit/pause icon right)
> 2. Habit name in serif display, with current streak count as a small badge
> 3. 30-day history strip as gentle dots (olive filled for check-in, sand for missed)
> 4. Stats summary: longest streak / completion rate / total check-ins (small grid)
> 5. Last 3 daily check-in notes if any (small cards)
> 6. Primary action button at bottom: "Check in for today"
>
> Content shape: 14-day current streak with 2 missed days in the last 30 days.
>
> Stay inside the visual grammar of the anchor. Don't introduce new color
> values. Use the same card style as the dashboard's habit cards. No charts
> or graphs — the dot strip is the only data viz.

## Notes
- The card style for the streak history is the highest-risk drift point. Explicitly reference the anchor's card pattern in the prompt.
- If the tool generates a chart instead of dots, redirect: "use a horizontal strip of small dots, not a chart."
```

### Pass 2 tool variations (short)

- **Claude Design:** paste the anchor mockup as an image into the conversation. Use inline comments to point at the anchor card style and say "reuse this exact card style for the streak history." Sliders are great here for tweaking the dot spacing in the history strip.
- **Stitch:** attach DESIGN.md (still in scope from Pass 1 if same session) and the anchor mockup as an image input. Keep the prompt focused on what this screen *adds* relative to the anchor. Use Zoom-Out-Zoom-In: confirm Stitch holds the anchor language, then zoom in on the history strip detail.
- **Open Design:** pick the `webapp-screen` or `detail-view` skill. Attach the anchor mockup as a reference image. Discovery form answers reuse Pass 1's brand/tone block — only "section list" changes.
- **v0:** include the anchor JSX as a code reference (paste a snippet of the dashboard's `<HabitCard>` component if v0 generated one). Specify framework versions. v0 will reuse the component for the streak history dots naturally.

---

## Pass 3 worked example — Empty dashboard state

Pass 3 is mechanical once Pass 2 exists. Reference the Pass 2 mockup; specify only the state-specific content from FEATURES § States.

### Pass 3 brief

```markdown
---
pass: 3
screen: dashboard
state: empty
surface: web-app
status: ready
output_path: docs/mockups/dashboard-empty.png
visual_reference: docs/mockups/anchor-dashboard.png
---

# Pass 3 Brief — Dashboard Empty State

## Surface and frame
- **Artifact type:** state variant of the dashboard
- **Output target:** `docs/mockups/dashboard-empty.png`

## State
empty — user has no habits yet (first run after onboarding)

## Visual reference
`docs/mockups/anchor-dashboard.png` — variant of this screen

## Sections / hierarchy (empty variant — only what differs from anchor)
1. **Top bar** — same as anchor
2. **Serif greeting strip** — same, but use "Good morning" without a name (onboarding may not have captured one yet)
3. **Empty-state hero** — replaces the three habit cards. Serif copy ("Start your first habit. Small, daily, gentle.") + one primary olive-accent button: "Add habit"
4. **Streak overview** — hidden
5. **Quick capture FAB** — hidden (the empty-state CTA replaces it)

## Do's and Don'ts
- **Do:** keep the empty state minimal — one decision (start a habit), one button.
- **Do:** keep the serif typographic moment — the empty state is a brand moment too.
- **Don't:** no marketing copy, no onboarding tips, no faded "what this could look like" preview. The brand is calm; first impression is calm.

## The prompt

> Generate the empty-state variant of the Streakly dashboard. Visual reference:
> [paste Pass 2 mockup]. State: user has no habits yet.
>
> Replace the three habit cards with: serif hero copy reading "Start your
> first habit. Small, daily, gentle." and a single olive-accent primary
> button labeled "Add habit." Hide the streak overview strip and the
> floating quick-capture button.
>
> Inherit everything else from the anchor — typography, spacing, color, card
> style. Keep the empty state minimal and quiet. No marketing copy, no
> onboarding tips, no faded preview of populated content.

## Notes
- The empty-state copy is fixed (from FEATURES § States). Render the typography around it without altering the text.
- The state should feel like the same screen, just quieter — not a different page.
```

### Pass 3 across tools

All four tools handle Pass 3 the same way — paste the Pass 2 mockup as visual reference, name the state, specify the state-only content. The brief above works in all four with no per-tool variation needed. If iterating, Claude Design's inline-comment feature is fastest for state-variant tweaks.

---

## Common iteration patterns

When the first output is off, these prompts narrow the gap.

### "The output feels too generic / corporate"

The tone ladder isn't pulling its weight. Strengthen:

> The tone is reading as generic SaaS, not editorial. Push further into the
> serif-led calm direction — think small literary magazine or independent
> publishing site, not Stripe-clone. The serif headlines should feel like
> intentional typographic craft, not just "use a serif font."

### "Wrong empty state behavior"

Check the brief — likely missing state copy from FEATURES.

> The empty state needs the exact copy from the feature spec:
> "Start your first habit. Small, daily, gentle." Don't paraphrase. Render
> the typography around that exact text.

### "Spacing feels off"

Direct the tool to the DESIGN.md section.

> Re-read DESIGN.md § Spacing — the base unit is 8px and the rhythm should
> use 8 / 16 / 24 / 32 multiples. Tighten vertical gaps between sections to
> 24px and component-internal padding to 16px.

### "Color drifted from anchor"

Add an explicit Don't.

> The output introduces a third color (a muted teal) that isn't in the brand
> palette. Strip it. The only colors are sand canvas, charcoal text, and the
> olive accent. The accent appears on the primary CTA only — nothing else.

### "Too many UI elements"

Simplify the section list and add a YAGNI directive.

> The section list is shorter than what you generated. Remove the secondary
> navigation, the search bar, and the notification bell. The top bar has
> only the wordmark and settings icon, nothing else. The brand is minimal —
> every additional element fights that.

### "Card style drifted between screens"

This is the highest-risk Pass 2 failure mode. Reference the anchor card directly.

> The card style in this screen doesn't match the anchor's habit cards. The
> anchor uses sand-canvas-on-sand-canvas cards with a subtle 1px charcoal
> outline and 12px radius. Match that exactly here — don't introduce a new
> card pattern.

### "Tool generated a chart when you wanted dots"

Be explicit about visual primitives.

> Don't use a chart or graph for the streak history. Use a horizontal strip
> of small circular dots — olive-filled for completed days, sand-colored
> outlined for missed days. 30 dots, evenly spaced, no axis lines, no labels.

---

## Quick reference — same brief, three surfaces

If you're working on a different surface than the web-app example above, here's the Pass 1 brief shape adjusted for each surface family.

### Web marketing (Pass 1, homepage)

- **Anchor:** homepage hero + above-fold sections
- **Sections to brief:** hero (H1 + sub + primary CTA + visual), social proof strip, value props (3-col), key feature spotlight, secondary CTA, footer
- **Tone in prompt:** lead with conversion intent, "this page's job is to convert visitors to signup"
- **Do's:** mobile-first thumb-reach CTA, outcome-focused H1 (<10 words), loss-aversion framing if applicable
- **Don'ts:** anti-startup-speak ("revolutionary", "world's first", "leveraging cutting-edge"), competing CTAs, multi-step lead forms

### Native iOS (Pass 1, two anchors)

- **Anchor 1:** home/today screen with tab bar visible
- **Anchor 2:** onboarding step 1 (welcome)
- **Why two:** in-app and onboarding have distinct visual languages — onboarding is often more cinematic, full-screen, high-contrast; in-app is calmer and inherits HIG conventions
- **Sections to brief (in-app):** status bar respect, navigation header (large title style), primary content area, tab bar (3–5 tabs)
- **Sections to brief (onboarding):** full-screen image or pattern, large serif headline, body copy, primary action, skip option in top-right
- **Tone in prompt:** mention HIG compliance ("respect safe areas, support Dark Mode, use SF Symbols where standard icons exist")
- **Do's:** thumb-reach primary actions in lower third, native components customized via theme tokens
- **Don'ts:** custom tab bars, custom alerts, hover states (no hover on touch), command palette (no keyboard)

---

## Maintenance notes

This file ages. Tools evolve — Claude Design just launched, Stitch is shipping changes, Open Design is fast-moving. When updating:

- Keep examples grounded in **structurally durable patterns** (e.g., "always attach DESIGN.md to Stitch") rather than **interface-specific moves** ("click the third toggle on the right panel"). Structural patterns survive UI changes.
- Update the **last-updated date** at the top when refreshing.
- The Streakly example project is generic on purpose — don't replace it with a real product unless that product becomes the canonical demo.
- If a fifth tool emerges (or one of the four deprecates), update the quickstart matrix first, then add or remove its slots in the Pass 1 / Pass 2 / Pass 3 examples.
