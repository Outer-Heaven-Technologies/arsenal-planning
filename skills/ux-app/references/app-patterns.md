# Web App UX Patterns Reference

> Patterns for authenticated web applications. Organized by app shell decisions, engagement models, common screen types, and anti-patterns by shape.

## How to use this file

This reference is consulted by `ux-app` after the app shape and engagement model have been classified. It provides:

1. **App shell patterns** — sidebar, top nav, banner, command palette
2. **Engagement model patterns** — ritual, workflow, reference, creation, monitoring
3. **Common screen patterns** — dashboard, list view, detail view, settings, onboarding, empty states
4. **Anti-patterns by app shape** — what NOT to do for productivity tools, dashboards, daily-drivers, etc.
5. **Industry skeleton examples** — full skeletons for canonical shapes (productivity tool)

Patterns are general — customize aggressively for the specific product.

## Index

- [App shell patterns](#app-shell-patterns)
- [Engagement model patterns](#engagement-model-patterns)
- [Common screen patterns](#common-screen-patterns)
- [Anti-patterns by app shape](#anti-patterns-by-app-shape)
- [Industry skeleton examples](#industry-skeleton-examples)

---

## App shell patterns

### Sidebar navigation

**When to use:** Apps with 4+ primary destinations, deep nested navigation, or workspaces with multiple top-level entities (projects, teams, channels).

**Best practices:**
- **Collapsible** — let power users collapse to icon-only mode
- **3–7 primary items at top level** — beyond that, group under sections
- **Color-code or icon-code groups** — group colors, project colors, status colors
- **Pin the user/account widget to the bottom** — settings, signout, current workspace
- **Show counts/badges sparingly** — only when actionable (unread, pending, due today)
- **Allow custom ordering** for apps with user-owned entities (projects, groups, boards)
- **Active state is unambiguous** — strong visual difference between current and other items

**Anti-patterns:**
- More than 10 items at top level — cognitive overload
- Two-row sidebars with conflicting hierarchy
- Icons without labels until hover — discoverability suffers
- Sidebar that resets state on every navigation

### Top navigation

**When to use:** Apps with 3–5 primary destinations and shallow hierarchy, or apps with a more marketing-feel where the top nav doubles as branding.

**Best practices:**
- Keep to 5 items maximum
- Right-side: user menu, notifications, primary CTA
- Left-side: logo + primary destinations
- Sticky on scroll

**Anti-patterns:**
- Mixing primary actions with destinations in the same nav
- Mega-menus inside an app (web apps aren't catalogs)

### Persistent banner / header strip

**When to use:** When there's *always-relevant* user-state context that orients work — current sprint, daily intention, status, financial state.

**Best practices:**
- Subtle — under 48px tall, low visual weight
- Click to expand for full context
- Editable inline or via modal
- Updates from a single canonical source

**Anti-patterns:**
- Banner used for marketing or upsells inside an app (massive trust killer)
- Banner that pushes content down without adding orientation value
- Multiple stacked banners

### Command palette (Cmd-K)

**When to use:** Any web app with more than ~10 screens or actions. Essential for power users.

**Best practices:**
- Single keyboard shortcut (Cmd-K on Mac, Ctrl-K on Windows)
- Search across: navigation destinations, entities, recent items, available actions
- Recent items at top when palette opens with empty query
- Show keyboard shortcuts inline so users learn them
- Allow quick-create from palette (e.g., "Task: Draft Praxis landing page" → creates a task)

**Anti-patterns:**
- Palette that only does navigation (missing the action layer)
- Palette without recent items
- Palette that doesn't appear above modals/sheets (becomes unreachable in flow)

### Global quick-create

**When to use:** Apps where the user creates entities frequently and doesn't want to navigate to a specific screen first.

**Best practices:**
- Single button or shortcut (typically `c` or `n`)
- Picker for entity type if multiple types are creatable
- Modal or sheet rather than full navigation
- Pre-fills context where sensible (creating a task while inside a project? default parent = that project)

---

## Engagement model patterns

### Ritual model

**Use for:** Daily-driver apps where the user opens at predictable moments and follows a structured flow. Examples: journaling apps, morning planning tools, end-of-day reflection apps.

**Best practices:**
- **Predictable entry point** — same screen every morning/evening
- **Structured multi-step flow** — one section at a time on mobile, cohesive scroll on desktop
- **Visible progress** — step indicator or progress dot
- **Resume in progress** — if user starts and walks away, return to the same place
- **Carry-forward state** — last session's data informs this session's defaults (e.g., yesterday's evening priority → today's morning Big 3)
- **Save throughout, not only at end** — protects against abandonment
- **Auto-context from data** — the ritual surfaces relevant numbers from the period without asking the user to fetch them

**Anti-patterns:**
- One giant form with 30 fields — kills engagement
- Required validation that blocks completion — let users save partial entries
- Streak-based gamification with shame consequences — proven retention killer for habit-style products
- Ritual that takes longer than its stated time budget — promise 5 minutes, deliver 5 minutes

### Workflow model

**Use for:** Apps where the user opens repeatedly to complete specific tasks throughout the day. Examples: task managers, customer support tools, code editors.

**Best practices:**
- **Default to the inbox/queue** — what needs doing now
- **Multiple view modes** for the same data (list, board, calendar, timeline)
- **Persistent filters** — remember the user's last filter state
- **Bulk operations** — multi-select for power users
- **Keyboard shortcuts** for status changes, moving items, navigation
- **Status changes are reversible** — undo any state change for at least 5 seconds

**Anti-patterns:**
- Default view loaded with sample/marketing data
- Status changes that don't reflect in dependent views immediately
- Hidden actions behind right-click only (discoverability)
- Slow page loads on the main work view

### Reference model

**Use for:** Screens where the user comes to look something up or check state. Examples: dashboards, analytics views, documentation, knowledge bases.

**Best practices:**
- **Headline metric first** — most important number/state at the top
- **Progressive disclosure** — summary first, drill-down on click
- **Fast load** — reference screens get checked frequently
- **Print-friendly or shareable** — link to a specific view state
- **Stable URLs** — bookmark-able state
- **Time-range selector** — the user always wants to control the period being viewed

**Anti-patterns:**
- Required interaction to see the headline value (e.g., must select a filter first)
- Charts without context — always show period, comparison, trend
- Real-time updates that distract — the user is reading, not monitoring

### Creation model

**Use for:** Tools where the user produces artifacts. Examples: design tools, document editors, code editors, music production.

**Best practices:**
- **Canvas-first layout** — most of the screen is the work surface
- **Tools available but not in the way** — sidebar/floating tool palettes that can hide
- **Autosave aggressive** — every keystroke if possible
- **Version history** — undo across sessions
- **Focus mode / distraction-free** — hide everything except canvas
- **Keyboard shortcuts** for every tool

**Anti-patterns:**
- Top nav that takes 80px of canvas real estate
- Modals that block the canvas during creation
- Saving prompts that interrupt flow

### Monitoring model

**Use for:** Apps where the user checks on something and acts on anomalies. Examples: ops dashboards, alerting tools, infrastructure monitoring.

**Best practices:**
- **At-a-glance status** — green/yellow/red signal up top
- **Anomaly highlighting** — draw attention to things that need action
- **Drill-down to detail** — click anything to investigate
- **Alert configuration nearby** — make it easy to tune signal-to-noise
- **Time-range and refresh rate user-controlled**

**Anti-patterns:**
- Auto-refreshing UI that pushes content (creates loss of context)
- Alert badges without an action path
- Charts that scroll laterally without context anchors

---

## Common screen patterns

### Dashboard / home screen

**Purpose:** Orient the user. Show the current state of their work. Surface what needs attention.

**Sections in display order:**
1. **Greeting or anchor context** — optional but humanizing
2. **Headline state** — the single most important thing right now
3. **Today / active work** — what's in flight
4. **Metrics rollup** — cards showing key numbers by area
5. **Recent activity** — what's changed recently
6. **Quick actions** — shortcuts to common workflows

**Empty state:** Educational. Show what this dashboard will look like populated, with a CTA to set up the first item that drives the metrics.

**Anti-patterns:**
- Dashboard with 12 widgets — pick 3-5 that matter
- Dashboard that opens to a "configure your dashboard" prompt
- Metrics without comparison context (no "vs last week" or trend arrow)

### List view

**Purpose:** Browse and triage many items of the same type.

**Sections in display order:**
1. **Filter/search bar** — at the top
2. **View mode toggle** (list/board/calendar/tree) — if multiple views available
3. **Bulk action bar** — appears when items selected
4. **The list itself** — paginated or virtual-scrolled
5. **Pagination or "load more"** — bottom

**Empty state:** Single-CTA. One clear action to create the first item.

**Anti-patterns:**
- Filters that don't persist
- Sort options that reset on navigation
- Infinite scroll without "back to top"
- Pagination that loses the user's scroll position on return

### Detail view

**Purpose:** Deep work on a single item. Edit metadata, view history, see related items.

**Patterns:**
- **Full route** — own URL, deep linkable, browser back works
- **Sidebar panel** — opens beside the list, list remains visible
- **Modal / sheet** — overlays, dismissible

For complex apps, consider all three depending on action depth. Linear uses sidebar panel for most things; full route for the deepest work.

**Sections in display order:**
1. **Header** — title, status, primary action
2. **Metadata strip** — type, assignee, dates, links
3. **Body** — description, content, the meat
4. **Related items** — sub-tasks, comments, history
5. **Activity log** — at the bottom or in a tab

**Empty state:** N/A for new items being created. For non-existent items, redirect to list view with a flash message.

**Anti-patterns:**
- Modal detail view with no deep link
- Detail view that doesn't show parent context (no "← Back to project")
- Edit mode that requires a separate save button — inline edits are better

### Settings

**Purpose:** Configure the app. Manage account, preferences, integrations.

**Patterns:**
- **Linear** — single scrolling page with sections. Good for ≤10 settings.
- **Tabbed** — left sidebar nav with content panes. Good for 10–30 settings.
- **Search-first** — open with a search bar that filters settings. Good for 30+ settings.

**Sections in typical order:**
1. **Profile / account**
2. **Preferences**
3. **Integrations / connections**
4. **Notifications**
5. **Billing** (if applicable)
6. **Danger zone** — delete account, reset data — last

**Empty state:** N/A — settings always have defaults.

**Anti-patterns:**
- Settings buried 3+ clicks deep
- "Apply" buttons per section instead of save-on-change
- Confusing toggle states (off-by-default vs on-by-default not consistent)

### Onboarding (first-run)

**Purpose:** Get the new user from signup to first meaningful action.

**Patterns:**
- **Progressive disclosure** — multi-step flow that captures necessary setup
- **Empty-state-driven** — drop the user into the empty app shell and use empty states to guide them
- **Demo-first** — pre-populate with sample data, let them edit
- **Personalize-first** — ask a few questions, customize the experience based on answers

**Best practices:**
- **Activation event clearly defined** — what's the "first meaningful action" that signals the user got the value?
- **Skippable** — every step has a skip option
- **Show progress** — step indicator
- **Defer permissions** — don't ask for calendar access on screen 1; ask when calendar feature is being used
- **Personalization that matters** — every question should change the experience; if not, remove it

**Anti-patterns:**
- Onboarding that takes >2 minutes
- Onboarding that hides the actual app behind a 5-step questionnaire
- Onboarding that asks for permissions or payment before showing value

### Empty states

The single most important screen pattern in web apps. Every list/board view, every dashboard, every detail view of a non-existent thing needs a designed empty state.

**Patterns:**

- **Educational empty state** — "This is where your X will appear once you create some. Here's why you might want to."
- **Sample data empty state** — pre-populated with example data clearly labeled "Sample" so the user sees what populated looks like
- **Single-CTA empty state** — one obvious action, minimal explanation. Use when the next action is unambiguous.
- **Onboarding empty state** — guided creation of the first item

**Best practices:**
- Always include a CTA — never just a blank screen with "no items"
- Use friendly, contextual copy (not "Empty" or "No data")
- Illustrate where appropriate (but don't over-decorate)
- For data that takes time to populate (analytics, integrations), explain the wait

**Anti-patterns:**
- Empty list rendered as an actual blank screen — biggest retention killer
- Sample data without labels — confuses users about real state
- Empty state that requires reading 4 paragraphs before acting

---

## Anti-patterns by app shape

### Productivity tool anti-patterns

- **Streak-based gamification with shame consequences.** Proven retention killer for habit-style products — users abandon when they break the streak.
- **Forcing schema design on the user.** "Create your first database" / "Configure your first board" is high friction. Opinionated defaults win.
- **Sub-task hierarchies more than 3 levels deep.** Users get lost; navigation gets painful.
- **Kanban with too many statuses.** 4–6 columns max. 10 columns is information overload.
- **Notifications for every change.** Productivity apps get muted faster than any other category.

### Daily-driver / ritual app anti-patterns

- **Generic productivity-app aesthetic.** Calm, monastic, opinionated wins for this category (Things 3, Sunsama). Loud, gamified, dashboard-y loses.
- **Empty-state-on-day-one without onboarding.** A ritual app with a blank screen on first open is dead on arrival.
- **Required completion of every field.** Some days the user wants to do half the ritual. Let them.
- **Surfacing analytics during a ritual.** The ritual is for reflection, not for staring at charts.
- **Breaking the consistency of the ritual flow.** If a daily check-in has 7 sections this week and 5 next week, users lose the muscle memory.

### Dashboard / analytics anti-patterns

- **Loading state without skeleton.** Spinning indicators on data-heavy views kill perceived performance.
- **Real-time updates that move things.** Charts that auto-update shift the viewer's focus mid-read.
- **Drill-down without breadcrumbs.** User can't get back to where they were.
- **No way to share a specific view state.** URLs should encode filters, time range, and selection.
- **Color used as the only differentiator.** Accessibility issue and information loss.

### Content creation tool anti-patterns

- **UI chrome eating canvas.** Tool palettes, headers, sidebars should collapse or hide.
- **Save buttons.** Auto-save aggressively. Manual save is a 2010 pattern.
- **Modal dialogs during creation flow.** Block focus. Use sheets, panels, or inline.
- **No version history.** Users will lose work and lose trust.
- **No undo across sessions.** Limit to current session is frustrating.

### Internal admin tool anti-patterns

- **Generic Bootstrap admin template feel.** Internal users notice quality. Invest in craft.
- **Bulk operations buried in menus.** Internal tools live on bulk ops; surface them.
- **No permissions model.** The CEO and the intern shouldn't see the same screen.
- **Confirm dialogs for every destructive action without context.** Show what's being deleted, not just "Are you sure?"
- **Search that doesn't span entity types.** Internal tool users want to search globally.

### Marketplace / catalog anti-patterns

- **Search without filters.** Browsing depends on faceted filtering.
- **Detail views that don't show "you might also like."** Discovery is the value.
- **Cart/wishlist without persistence.** State must survive across sessions.

---

## Mobile considerations for web apps

Every web app screen should be evaluated for mobile (PWA) use. Common adaptations:

- **Sidebar collapses to bottom tab bar** (3-5 items) or hamburger menu
- **Modal/sheet replaces sidebar panel** for detail views
- **Multi-step replaces multi-column** for long forms
- **Persistent banner shrinks or disappears** on small screens
- **Cmd-K becomes a search button** in the header
- **Bulk actions become swipe gestures** or long-press

Decide per screen: is this usable on mobile? What changes? Document it in `UX.md`.

---

## Industry skeleton examples

### Productivity Tool

**Engagement model:** Workflow (primary), Reference (secondary on dashboards)
**Shell:** Sidebar with collapsible groups; Cmd-K palette; global quick-create
**Activation event:** First task / first note / first habit logged

**Screen inventory:**

- **Onboarding** — 3-4 screens max. Permission prompts deferred until first relevant use. Quick first action ("create your first task") anchors activation.
- **Home / today** — orient view. Today's work + recent activity + quick capture entry point.
- **Main list/board view** — primary work surface. Filters persist; bulk operations available; keyboard shortcuts for status changes.
- **Detail view** — single-item edit (sidebar panel or full route). Inline edits, autosave, related items below.
- **Quick capture modal** — invoked from anywhere via shortcut. Minimal fields. Pre-fills context.
- **Settings** — profile, preferences, integrations, notifications, billing, danger zone (in that order).
- **Search results** — global search across entity types.

**Components needed:**
- Sidebar with collapsible groups + quick-create button
- Cmd-K palette
- List row with status pill, drag handle, due date
- Kanban board with WIP-limit indicator
- Detail panel with autosave indicator
- Quick capture modal

**Anti-patterns:**
- Onboarding requires schema design ("name your first board")
- Empty home screen with no demo data and no onboarding
- Status changes not reflected in dependent views immediately
- Streak-based shame mechanics
- Bulk operations hidden behind right-click only

**Cross-references:**
- DESIGN_SYSTEM.md → List row, status pill, kanban column, detail panel, Cmd-K palette
- ARCHITECTURE.md → Sync architecture, offline-first considerations, integration APIs

---

## Cross-references

This file is consumed by the `ux-app` skill. Patterns here inform the generation of `UX.md`. Each pattern should reference back to the screen role and engagement model it applies to.
