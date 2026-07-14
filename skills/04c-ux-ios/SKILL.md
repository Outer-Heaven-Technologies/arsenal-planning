---
name: ux-ios
description: >
  Generate `UX.md` defining the UX architecture for a native iOS app. Covers screens, navigation pattern (TabView vs NavigationStack), onboarding flow, paywall strategy, permission timing, notification trust-earning, and activation model. Follows Apple's Human Interface Guidelines for navigation conventions, modal patterns, and platform expectations. Use whenever the user is planning a native iOS app — productivity, fitness, journaling, social, content, utility, or subscription. Triggers include "plan the iOS UX," "scaffold the iPhone app," "what screens does this iOS app need," "design the onboarding flow," "plan the paywall strategy." Do NOT use for marketing websites (use `ux-web`) or web applications (use `ux-app`).
---

# Plan UX (iOS)

Generate a `UX.md` file: the UX backbone of a native iOS app. iOS apps have specific concerns that don't apply to websites or web apps — onboarding flows that earn trust before paywalls, permission timing tied to value moments, notification opt-in earned through use, and platform conventions from Apple's Human Interface Guidelines that users expect.

**Use this skill for:**
- Native iOS apps (Swift/SwiftUI, React Native targeting iOS, Flutter targeting iOS)
- Subscription apps (StoreKit, RevenueCat, Superwall)
- Productivity, fitness, journaling, mood, habit apps
- Social and content apps
- Utility apps

**Do NOT use this skill for:**
- Marketing websites → use `ux-web`
- Web applications → use `ux-app`
- Android-only apps — this skill is iOS-specific (separate `ux-android` could exist)

**UX vs UI separation:**
- `UX.md` = UX — screen inventory, navigation hierarchy, onboarding flow, paywall placement, permission strategy
- `DESIGN_SYSTEM.md` = UI — visual design (SF Symbols usage, color, typography, motion)

Generate `UX.md` first. `DESIGN_SYSTEM.md` references it.

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

Extract what's already known: product description, target user, feature set, monetization model.

### Step 2: Classify the app shape

iOS apps have distinct shapes that determine navigation pattern and engagement model:

| Shape | Description | Examples | Primary engagement |
|---|---|---|---|
| **Single-purpose utility** | One main job, minimal navigation | Calculator, flashlight, timer | Quick interaction |
| **Productivity tool** | Manages user's work or tasks | Things 3, Drafts, Bear | Daily-driver workflow |
| **Daily-driver / ritual** | Repeat structured use, often reflective | Stoic, Day One, Streaks | Ritual completion |
| **Content consumption** | Browse and consume content | Reeder, Spotify, Apollo | Sustained sessions |
| **Content creation** | Produce artifacts | Procreate, Ferrite, Photoshop | Focused work sessions |
| **Social** | Communication with others | Messages, Discord, BeReal | Continuous low-attention |
| **Fitness/wellness** | Health tracking, workouts, mindfulness | Strava, Calm, Streaks | Daily engagement |
| **Subscription utility** | Specialized tool monetized via subscription | 1Password, Bear, MoneyMoney | Activation → retention |

Identify the primary shape and any blended secondary.

### Step 3: Determine monetization model

iOS apps almost always have a monetization model that shapes the onboarding and screen flow:

- **Free** — no payment, no IAP, possibly ads
- **One-time purchase** — paid app, no subscription
- **Freemium with subscription** — free tier + premium upgrade
- **Subscription with free trial** — most common modern model
- **Hard paywall** — must subscribe to use
- **Soft paywall** — limited free tier, paywall appears at engagement moments

The choice cascades into onboarding length, paywall placement, and what users see on first launch. Note: this skill helps design the UX; pricing strategy is a separate concern.

### Step 4: Choose primary navigation pattern

iOS has two dominant patterns; pick one as the primary:

**TabView (tab bar at bottom):**
- Best for: 3–5 primary destinations the user moves between regularly
- Standard for: most apps with multiple top-level areas
- Examples: Apple Music, Twitter, most productivity apps
- Tab count: 3–5 maximum (5 is "More" pushing the limit)

**NavigationStack (push/pop hierarchy):**
- Best for: Apps with clear hierarchy where the user drills in and out
- Standard for: settings flows, list-detail patterns
- Examples: Mail, Messages
- Often combined with TabView (each tab has its own nav stack)

**Hybrid:**
- TabView at root, NavigationStack within each tab
- Sheets and modals layered on top for focused tasks
- This is the modern default for most apps

**Decide explicitly:** primary pattern, number of tabs (if TabView), what's in each tab.

### Step 5: Design the onboarding flow

iOS app onboarding has a specific shape proven across thousands of subscription apps. The 9-step framework (adapt as needed):

1. **Welcome / value hero** — One outcome-driven headline, one primary CTA, no nav clutter
2. **The "why this app" screen** — Problem/solution framing in 2–3 panels
3. **Goal question** — "What brings you here?" (Lose weight / Build muscle / etc.) — frames everything downstream
4. **Profile / constraints** — Quick contextual questions (experience level, time available, goals)
5. **Permissions ask 1** — Only the permission you need now (notifications usually deferred)
6. **Plan preview** — "Here's your personalized [X]" — turn answers into a clear plan
7. **Social proof / reviews** — App Store ratings, testimonials, user counts
8. **Paywall** — Trial-based, clear pricing, terms/privacy links
9. **First app use** — Drop into the actual app

**Rules:**
- **If a question doesn't change anything in the experience, remove it.** Don't ask for the sake of asking.
- **Step indicator visible** ("Step 2 of 5") — finite-feeling onboarding completes more
- **Each step has skip** — paradoxically increases completion
- **Permissions only at value moments** — notifications usually after first use, not before
- **Paywall after value demonstrated** — onboarding builds the case; paywall is the close

Customize the framework for the app:
- Single-purpose utility: skip steps 3-4 (no personalization needed)
- Content consumption: emphasize content preview over personalization
- Social: defer paywall, prioritize friend-finding/connection
- Fitness: heavy personalization (steps 3-4 expanded)

### Step 6: Define paywall strategy

Subscription apps live or die on paywall strategy. Decide:

**Paywall type:**
- **Hard paywall** — must subscribe to use. Filters for high-intent users. Lower install-to-trial, higher trial-to-paid.
- **Soft paywall** — free tier with premium upgrade. Lower barrier, higher install retention.
- **Metered paywall** — free for N days/uses, then paywall. Apple-acceptable if disclosed.
- **Contextual paywall** — appears at gated features only.

**Paywall placement (often multiple):**
- **Post-onboarding** — most common. ~50%+ of conversions happen here.
- **Contextual** — at moment of value (locked feature tap, limit hit)
- **Settings** — for users who want to upgrade later
- **Promo / campaign** — special offers, win-back

**Paywall structure (Apple-compliant):**
1. **Value proposition** — what they're paying for
2. **Visual demonstration** — screenshots, animation, key benefits (3-4 max)
3. **Social proof** — ratings, user counts
4. **Plans** — 2-3 tiers max, with one labeled "Most Popular" or "Best Value"
5. **CTA** — clear text matching action ("Start Free Trial," "Subscribe")
6. **Trial disclosure** — duration, billing date, cancellation info
7. **Required links** — Privacy Policy, Terms of Use, Restore Purchases

**Apple paywall compliance (avoid rejection):**
- ❌ No toggle-based free trial designs that hide trial by default
- ❌ No misleading monthly breakdown for annual plans
- ❌ Missing Terms of Use / Privacy Policy links
- ❌ Auto-renewal terms not clearly disclosed
- ❌ Free trial CTA without explaining what happens after
- ✅ Clear plan list with actual billing cycle for each
- ✅ Trial duration and post-trial price clearly stated
- ✅ Restore Purchases button visible

### Step 7: Plan permission strategy

Permission timing is one of the biggest iOS UX decisions. Asking too early kills install retention; asking too late causes feature confusion.

**Permission timing rules:**

- **Notifications** — DEFER. Ask after first meaningful action ("Want a reminder tomorrow morning?"). Pre-permission upsells improve grant rate significantly.
- **Camera/Photos** — Ask at the moment of use (user taps "Add photo"). Never upfront.
- **Location** — Ask at the moment of use. Use "When in Use" not "Always" unless the value is clear.
- **HealthKit** — After demonstrating value. Use specific permissions, not "All."
- **Contacts** — Ask only if essential. Many apps overreach here.
- **Microphone** — At moment of use.
- **Bluetooth** — At moment of use.

**Permission patterns:**

1. **Pre-permission screen** — App's own UI explaining why, with "Continue" → triggers system dialog. Increases grant rate.
2. **Just-in-time** — System dialog appears at moment of use, no pre-screen. Faster but lower grant rate.
3. **Settings deep-link** — When permission denied, deep-link to Settings to re-grant.

For each permission the app needs, document: which moment in the user flow triggers it, whether a pre-permission screen is used, and what the fallback is if denied.

### Step 8: Generate screen inventory

For each app shape, common screens. Customize aggressively. Industry-specific full skeletons (habit tracker, meditation, mood tracker) live in `references/ios-patterns.md` — consult those after classification.

**Daily-driver / ritual app baseline:**
- **Onboarding flow** — multi-step (see Step 5)
- **Home / today screen** — orienting view, what's happening today
- **Ritual flow** — multi-step structured input (e.g., morning check-in, evening reflection)
- **Detail / entry view** — viewing past entries
- **List / history** — browse past sessions
- **Stats / progress** — visualization of trends
- **Settings**
- **Paywall** (post-onboarding + contextual)

**Productivity tool baseline:**
- **Onboarding**
- **Inbox / today** — what needs attention
- **List/board view** — primary work view
- **Detail view** — single item
- **Quick capture** — keyboard shortcut or floating action
- **Settings**

**Content consumption baseline:**
- **Onboarding** (often shorter — focus on content preview)
- **Feed / discover** — main browse view
- **Detail / reader view** — consuming a single piece
- **Library / saved** — user's collected content
- **Search**
- **Settings**

For each screen decide: full route (push), sheet (modal), or full-screen cover (takeover). iOS conventions:

- **Push (NavigationStack)** — drilling deeper into hierarchy (list → detail)
- **Sheet (.sheet)** — modal task with clear dismiss (compose, edit, share)
- **Full-screen cover (.fullScreenCover)** — onboarding, paywall, immersive flows where back-swipe should be blocked

### Step 9: Per high-stakes screen, define

For each main screen, document:

- **Screen role** — what is this for? (onboard / orient / capture / browse / configure)
- **Engagement model** — ritual / workflow / reference / creation / monitoring
- **Primary action** — single most important action available
- **Activation event** (for onboarding screens) — what does success on this screen look like?
- **Sections in display order** — top-to-bottom layout with rationale
- **Components needed** — names for DESIGN_SYSTEM.md
- **Permissions triggered here** — any system dialogs
- **Empty state** — what does this screen look like with no data?
- **Anti-patterns** — what NOT to do for this screen type

**Output format per screen:**

```markdown
## [Screen name]

**Screen role:** [onboard | orient | capture | browse | configure | paywall]
**Navigation:** [tab root | push from X | sheet from Y | full-screen cover]
**Engagement model:** [ritual | workflow | reference | creation | monitoring]
**Primary action:** [the single most important thing the user does here]

### Sections (in display order)
1. **[Section name]** — [one-line rationale]
...

### Components needed
- [Component name] — [description]
- ...

### Permissions triggered
- [Permission] — [when, with pre-permission screen or just-in-time]

### Empty state
- **Pattern:** [educational | sample-data | single-CTA | onboarding]
- **CTA:** [primary action available in empty state]

### Anti-patterns
- [What not to do] — [why]

### Cross-references
- ARCHITECTURE.md → [relevant entity, data flow]
- DESIGN_SYSTEM.md → [relevant components, SF Symbols]
```

### Step 10: Apple Human Interface Guidelines compliance check

Cross-reference the skeleton against HIG (https://developer.apple.com/design/human-interface-guidelines):

- **Navigation** — TabView uses 3-5 tabs; NavigationStack for hierarchy; sheets for modals
- **Modals** — full-screen covers blocking dismiss should be used sparingly (onboarding, paywall)
- **System UI** — use SF Symbols where standard icons exist; respect Dynamic Type; support Dark Mode
- **Gestures** — back-swipe should work on NavigationStack pushes
- **Status bar** — visible by default; only hide for immersive content
- **Safe areas** — respect notch, home indicator, status bar
- **Haptics** — use sparingly, only for meaningful confirmations

Flag any skeleton decisions that conflict with HIG conventions.

### Step 11: Review & cross-check

After generating, tell the user:

- Where the file was created
- Navigation pattern chosen (TabView/NavigationStack/Hybrid)
- Number of onboarding steps
- Paywall strategy summary
- Permissions and their timing
- If `.arsenal/design/DESIGN_SYSTEM.md` exists: list components named that don't have entries
- Offer to flesh out any screen needing more detail

## File placement

```
project-root/
└── .arsenal/
    └── design/
        └── UX.md
```

## Important guidelines

- **HIG compliance is non-optional for App Store approval.** Skeleton decisions that conflict with HIG should be flagged.
- **Onboarding is the conversion engine for subscription apps.** 50%+ of trial starts happen here. Get it right.
- **Permissions are trust events.** Each permission ask has a cost. Defer until value is clear.
- **Paywall compliance is App Store-make-or-break.** Apple rejects toggle-hidden trials, missing terms links, misleading pricing. Build to compliance.
- **Empty states matter even more on iOS.** Smaller screens, less room for clutter. A good empty state is one screen with one clear CTA.
- **Tab bar is sacred.** Don't put 6 tabs. Don't change tabs based on state. Don't use tab badges as marketing.
- **Sheets vs. covers.** Sheets are dismissible (swipe-down); full-screen covers are not. Use covers only when you must (onboarding, paywall).
- **Mobile-first means thumb-first.** Primary actions in the lower third. Critical taps within thumb reach.
- **Native components when possible.** Custom navigation, custom alerts, custom tab bars erode the native feel. Use Apple's components and customize the visual layer in `DESIGN_SYSTEM.md`.
- **Don't import web-app thinking.** No command palette (no keyboard), no sidebar on phone (tab bar instead), no hover states (none on touch).
- **Cap `UX.md` at ~500 lines.** If it would exceed, split into a parent `UX.md` (screen inventory + nav + global anti-patterns) plus per-screen sub-files in a `ux/` folder (e.g., `ux/onboarding.md`, `ux/paywall.md`) and cross-link.
