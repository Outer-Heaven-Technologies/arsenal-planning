# iOS App UX Patterns Reference

> Patterns specific to native iOS apps. Organized by navigation patterns, onboarding flows, paywall strategies, permission strategies, and anti-patterns by app shape, plus industry skeleton examples.

## How to use this file

This reference is consulted by `ux-ios` after the app shape and monetization model have been classified. It provides:

1. **Navigation patterns** — TabView, NavigationStack, sheets, full-screen covers
2. **Onboarding flow patterns** — the 9-step framework, adaptations by shape
3. **Paywall patterns** — placement, structure, Apple compliance
4. **Permission patterns** — pre-permission, just-in-time, deferred
5. **Anti-patterns by app shape**
6. **Industry skeleton examples** — full skeletons for canonical iOS app shapes (habit tracker, meditation, mood tracker)

For Apple's authoritative reference: https://developer.apple.com/design/human-interface-guidelines

## Index

- [Navigation patterns](#navigation-patterns)
- [Onboarding flow patterns](#onboarding-flow-patterns)
- [Paywall patterns](#paywall-patterns)
- [Permission patterns](#permission-patterns)
- [Notification patterns](#notification-patterns)
- [Anti-patterns by app shape](#anti-patterns-by-app-shape)
- [Industry skeleton examples](#industry-skeleton-examples)

---

## Navigation patterns

### TabView (tab bar)

**When to use:** Apps with 3–5 primary, parallel destinations the user moves between regularly. The standard iOS pattern for most multi-area apps.

**Best practices:**
- **3–5 tabs maximum** — beyond 5, use a "More" tab that pushes the limit
- **Persistent across the app** — don't hide the tab bar on most screens
- **SF Symbols for icons** — use the platform vocabulary
- **Selected state unambiguous** — color shift + filled icon variant
- **Tabs are siblings, not parent-child** — don't use tabs for hierarchy
- **Each tab has its own NavigationStack** — preserves state when switching tabs

**Anti-patterns:**
- Tab bar that disappears in some screens (creates lost-context feel)
- Using tabs as navigation hierarchy ("step 1, step 2, step 3" as tabs)
- Badge numbers used as marketing ("Try our new feature!")
- More than 5 visible tabs

### NavigationStack (push/pop)

**When to use:** Hierarchical drill-down — list to detail, settings to sub-settings.

**Best practices:**
- **Back button visible at top-left** by default — never hide it
- **Title in the navigation bar** identifies current location
- **Back-swipe gesture works** (don't override)
- **Pop to root** is one tap on the tab icon
- **Deep linking** should work from any screen

**Anti-patterns:**
- Disabling back-swipe (frustrates users used to the gesture)
- Push within a sheet (causes nav confusion)
- Missing back button or replacing with custom non-discoverable affordance

### Sheets (.sheet)

**When to use:** Modal task with clear dismissal — compose, edit, share, configure.

**Best practices:**
- **Swipe-down to dismiss** — standard behavior, don't break
- **"Cancel" or "Done" in nav bar** — explicit dismissal options
- **Use detents** (medium/large) for partial sheets when content is short
- **Form sheets for compose flows** — emails, posts, edits
- **Page sheets for content** — viewing a detail, picking an option

**Anti-patterns:**
- Disabling swipe-down dismiss (frustrates users)
- Sheets stacked 3+ deep (lose context)
- Sheets with their own tab bar (over-engineered)

### Full-screen cover (.fullScreenCover)

**When to use:** Sparingly. Onboarding, paywall, immersive content (full-screen photo, video player).

**Best practices:**
- **Explicit dismiss button** — sheet behavior doesn't apply
- **Used for "takeover" moments** — when partial dismissal would harm the flow
- **Onboarding and paywall** are the classic use cases

**Anti-patterns:**
- Using full-screen cover when a sheet would suffice (over-aggressive)
- Full-screen cover without a clear dismiss path (traps the user)

---

## Onboarding flow patterns

### The 9-step framework

Proven across thousands of subscription apps. Adapt; don't follow blindly.

1. **Welcome / value hero**
2. **The "why this app" screens** (2–3 panels of problem/solution)
3. **Goal question** ("What brings you here?")
4. **Profile / constraints** (experience level, time, context)
5. **Permission ask 1** (only what's needed now)
6. **Plan preview** ("Here's your personalized X")
7. **Social proof** (ratings, reviews, user counts)
8. **Paywall** (trial-based, clear pricing)
9. **First app use**

### Adaptations by shape

**Single-purpose utility:**
- Skip steps 3-4 (no personalization)
- Often skip steps 7-8 (no subscription)
- 2-3 steps total, focused on showing the one thing the app does

**Productivity tool:**
- Steps 1-3 standard
- Replace step 4 with workspace/data setup
- Step 6 might be "Create your first [entity]"

**Daily-driver / ritual app:**
- Steps 1-2 emphasize the ritual outcome
- Step 3 captures motivation (why are you here?)
- Step 4 captures schedule preferences
- Step 6 sets up first ritual

**Content consumption:**
- Steps 1-2 show content preview
- Step 3 might be topic/category selection
- Skip step 4 (don't over-question)
- Step 6 shows curated feed based on selections

**Social:**
- Steps 1-2 emphasize connection
- Defer paywall significantly
- Friend-finding/connecting is step 6
- Notifications permission is critical here (often step 5)

**Fitness/wellness:**
- Steps 3-4 are heavy (goals, fitness level, schedule, equipment)
- HealthKit permission at step 5 with clear value
- Step 6 is the personalized plan

### Onboarding rules

- **Every question must change the experience.** If it doesn't, remove it.
- **Step indicator visible** — "Step 2 of 5" — finite onboarding completes more.
- **Skip button on every step** — paradoxically increases completion.
- **No more than 2 minutes total** — shorter for utilities, longer for personalized apps.
- **Save partial state** — if the user backgrounds the app, resume from where they were.
- **Activation event clearly defined** — what's the "first meaningful action" that signals onboarding success?

---

## Paywall patterns

### Paywall types

**Hard paywall** — must subscribe to use the app at all.
- Use when: app is purely a premium tool with no free use case
- Pros: filters for high-intent users
- Cons: lower install-to-trial; many users bounce
- Examples: 1Password, some pro tools

**Soft paywall** — free tier with premium upgrade.
- Use when: there's genuine free value
- Pros: higher install retention
- Cons: requires careful feature gating
- Examples: most productivity apps, most content apps

**Metered paywall** — free for N days/uses, then paywall.
- Use when: app needs trial period for value to land
- Apple compliance: must clearly disclose the meter
- Examples: news apps, some content tools

**Contextual paywall** — appears only at gated features.
- Use with: soft paywall as the model
- Pros: paywall feels earned (user already values the feature)
- Best practice: pair with a post-onboarding paywall for early conversion

### Paywall placement strategy

Most apps use multiple paywall placements:

- **Post-onboarding (highest converting)** — ~50%+ of trial starts. Place immediately after onboarding builds the value case.
- **Contextual** — when user taps a gated feature. Show only the relevant benefits.
- **Settings** — passive option for users who want to upgrade later.
- **Promo / campaign** — special offers, limited-time discounts.
- **Win-back** — for churned users on resubscription flow.

### Paywall structure

Sections in order:

1. **Value proposition** — one outcome-focused line
2. **Visual demonstration** — animation, screenshots, or 3-4 benefit bullets
3. **Social proof** — App Store rating, user count, named press if any
4. **Plans** — 2-3 tiers max, one labeled "Most Popular" or "Best Value"
5. **CTA** — clear text matching action ("Start Free Trial," "Subscribe for $X")
6. **Trial disclosure** — explicit: "X-day free trial, then $Y/year. Cancel anytime."
7. **Required links** — Privacy Policy, Terms of Use, Restore Purchases

### Apple paywall compliance (avoid rejection)

Common rejection reasons under Guideline 3.1.2:

- ❌ Toggle-based free trial designs hiding trial details
- ❌ Misleading monthly breakdown when charging annually
- ❌ Missing Terms of Use or Privacy Policy links
- ❌ Auto-renewal terms not clearly disclosed
- ❌ Free trial CTA without clear post-trial pricing
- ❌ "Subscribe" button that doesn't say it's a subscription

Compliance requirements:

- ✅ Plan list with actual billing cycle for each
- ✅ Trial duration and post-trial price clearly stated
- ✅ Restore Purchases button visible
- ✅ Cancellation instructions accessible (link to App Store subscription management)
- ✅ Match button text to action ("Start Free Trial" or "Subscribe" not "Continue")

### Paywall design 2026 trends

- **Animated paywalls convert ~3x static** — motion draws attention to key elements
- **Outcome-focused headlines** beat feature lists
- **3-4 benefits maximum** — users don't read more
- **Yearly plan default-selected** with "Most Popular" badge — guides decision
- **Trust signals near CTA** — "Cancel anytime," "No credit card to start trial"

---

## Permission patterns

### Permission timing rules

| Permission | When to ask | Pre-permission screen? |
|---|---|---|
| Notifications | After first meaningful action, or at value moment | Yes — large lift in grant rate |
| Camera | At moment of use (user taps "Add photo") | Optional |
| Photos | At moment of use | Optional |
| Location (When in Use) | At moment of use | Yes if value isn't obvious |
| Location (Always) | Only if essential; explain why | Required — high friction |
| HealthKit | After value demonstrated | Yes — must explain specific permissions |
| Contacts | Only if essential | Yes — many apps overreach |
| Microphone | At moment of use | Optional |
| Bluetooth | At moment of use | Optional |
| Tracking (ATT) | At moment relevant to user | Yes — explain value of personalization |

### Pre-permission screen pattern

Your own UI explaining *why* the permission is needed, with a "Continue" button that triggers the system dialog.

**Best practices:**
- One screen, one permission
- Outcome-focused copy ("Get reminded when X" not "Send notifications")
- Show a visual or example of the value
- Allow skip — never trap users

**Why it works:**
- The system dialog is binary; you only get one shot
- Pre-permission screen pre-qualifies users who actually want the permission
- Users who tap "Continue" then "Allow" are much more committed
- Grant rate often 30-50% higher with pre-permission

### Deferred permission pattern

For permissions not needed at onboarding, request at the moment they become useful.

**Examples:**
- Notification request after user creates first habit ("Want a reminder tomorrow?")
- Camera request when user taps "Add photo to entry"
- Location request when user opens a map feature

### Denied permission handling

When a permission is denied:
- Don't block the app from working — degrade gracefully
- When the user later tries to use the feature, show a helpful screen explaining and deep-linking to Settings
- "Open Settings" button that opens directly to the app's permission page

---

## Notification patterns

### Notification trust pyramid

1. **Earn the right to ask** — let the user complete first meaningful action
2. **Ask at the right moment** — when the value is clear ("Want reminders for X?")
3. **Earn the right to send** — only send high-signal, user-relevant notifications
4. **Earn the right to keep sending** — quality bar, never spam

### Notification types

- **Time-based reminders** — user-configured ("Remind me to journal at 9 PM")
- **Event-based** — something happened the user cares about (message, mention)
- **Insight notifications** — pattern observed worth surfacing
- **Re-engagement** — used carefully; backfires if overdone

### Notification anti-patterns

- Asking for permission on launch screen (worst grant rate of any pattern)
- Marketing-style notifications ("Try our new feature!")
- High-volume notifications (users mute or uninstall)
- Notifications without a clear destination when tapped

---

## Anti-patterns by app shape

### Subscription utility anti-patterns

- **Hard paywall before value demonstrated** — kills install retention
- **No free trial** for high-priced subscriptions
- **Hidden auto-renewal terms** — Apple rejects, users complain
- **Single plan with no comparison** — denies users the "Best Value" decision
- **Aggressive notification opt-in on launch**

### Daily-driver / ritual app anti-patterns

- **Streak-based shame mechanics** — proven retention killer
- **Empty home screen on first launch with no onboarding**
- **Forcing all permissions upfront** (kill install retention)
- **Paywall before user completes first ritual**
- **Notifications that interrupt rather than support the ritual**

### Productivity tool anti-patterns

- **Onboarding that requires schema design** ("Create your first board")
- **Empty inbox/queue with no sample data and no onboarding**
- **Hidden quick-create** (no floating action, no swipe-create)
- **No keyboard shortcuts for power users** (even on iOS — external keyboards)

### Content consumption anti-patterns

- **Onboarding before content preview** — users want to see content first
- **Login wall before browsing** — kills install conversion
- **Endless scroll without "back to top"**
- **No share extension** (iOS users expect share-from-anywhere)

### Social app anti-patterns

- **Asking for Contacts permission immediately** — high overreach perception
- **Paywall before connection** — defer monetization
- **Tab bar without notifications/inbox tab** — users expect it

### Fitness/wellness app anti-patterns

- **HealthKit permission with all access requested** — use granular permissions
- **No demo workout/session before paywall**
- **Onboarding that doesn't personalize the plan**
- **Notifications that shame missed workouts**

---

## Industry skeleton examples

Full iOS app skeletons for canonical shapes. Use as a structural template; customize for the specific product.

### Habit Tracker

**Engagement model:** Activation-driven (first habit logged), streak-anchored retention
**Monetization:** Freemium with subscription, paywall after value demonstrated
**Navigation:** TabView (Today / History / Insights / Settings)
**Style direction:** Claymorphism + Vibrant Block-based, streak warm tones (amber/orange) + progress green + motivational accents, playful rounded friendly typography

**Screen inventory:**

1. **Onboarding** — 3-4 screen value-prop tour, then immediate first-habit setup. Permissions (notifications) requested at moment of utility, not upfront.
2. **Today view** — Today's habits, large tap targets for "done." This is where users live.
3. **Habit detail / history** — Calendar heatmap, streak count, completion rate. Anchors emotional investment.
4. **Insights / stats** — Weekly/monthly trends, longest streak, completion rate by habit.
5. **Add habit flow** — Quick form. Frequency, time of day, optional reminder.
6. **Paywall** — Triggered after first streak milestone (3-7 days), not on launch. Pre-selected annual plan, free trial offered.
7. **Settings** — Account, notifications, themes, restore purchase.

**Components needed:**
- Today view list — large checkable rows
- Calendar heatmap
- Streak counter component
- Habit creation form
- Activation-triggered paywall sheet

**Anti-patterns:**
- Muted colors or low energy — kills the motivational quality
- Pushing paywall on launch — destroys activation rate
- Complex initial habit setup — should be one habit, one tap, two seconds
- Punishing or shame-based messaging on missed days — well-documented retention killer
- Notifications without earned trust (asking on first launch)

**Cross-references:**
- DESIGN_SYSTEM.md → Today view row, streak counter, heatmap, paywall sheet, motivational color tokens
- ARCHITECTURE.md → Local storage (Core Data/SwiftData), notification scheduling, IAP integration, optional cloud sync

---

### Meditation & Mindfulness

**Engagement model:** Story-led emotional resonance, ritual completion
**Monetization:** Free trial / freemium with paywall after first session
**Navigation:** TabView (Today / Library / Progress / Settings)
**Style direction:** Neumorphism + Soft UI Evolution, ultra-calm pastels (lavender/sage/sky) with breathing animation gradient, subtle soft monochromatic typography

Emotional design carries more weight than feature lists.

**Screen inventory:**

1. **Onboarding** — Goal selection (sleep, stress, focus, anxiety), experience level. Notifications optional, not aggressive.
2. **Home / today** — One recommended session prominently surfaced + library entry points.
3. **Session player** — Full-screen, distraction-free. Breathing animation, audio controls minimal.
4. **Library** — Browse by category, teacher, length, mood.
5. **Progress / streak** — Soft, encouraging. Total minutes meditated, current streak. Never shame-based.
6. **Paywall** — After first completed session. Annual plan with free trial. Soft, not aggressive.
7. **Settings** — Preferences, downloads (offline), reminders, restore purchase.

**Components needed:**
- Session card — soft shadow, image, title, duration
- Session player — full-screen with minimal controls
- Breathing animation component
- Library browse grid
- Soft paywall sheet

**Anti-patterns:**
- Inconsistent styling — breaks the calm
- Poor contrast ratios — soft palettes often fail accessibility; check WCAG
- Aggressive notifications or guilt-trip messaging
- Overly clinical or "wellness-industrial" feel
- Loud animations or jarring transitions

**Cross-references:**
- DESIGN_SYSTEM.md → Soft shadow tokens, breathing animation, session player, ambient gradients
- ARCHITECTURE.md → Audio streaming/download, IAP, offline storage, optional cloud sync for progress

---

### Mood Tracker

**Engagement model:** Daily check-in habit + insight reveal
**Monetization:** Freemium, paywall on advanced insights (after 7-14 days of usage)
**Navigation:** TabView (Today / History / Insights / Settings)
**Style direction:** Soft UI Evolution + Minimalism, emotion gradient (blue sad → yellow happy) + pastel per mood + insight accent, professional clean hierarchy

Sits between productivity (data tracking) and wellness (emotional outcomes). The defining UX: the daily check-in must take under 30 seconds.

**Screen inventory:**

1. **Onboarding** — Brief value prop, mood scale calibration, optional category setup (energy, anxiety, etc.). First check-in immediately.
2. **Daily check-in** — Mood selector (5-7 options with emoji or color), optional note, optional tags. Under 30 seconds total.
3. **Today / dashboard** — Today's mood, recent trend, prompt for any missed check-ins.
4. **History / calendar** — Calendar view colored by mood. Tap any day for the entry.
5. **Insights** — Trend charts, correlations (mood vs. sleep, exercise, weather if integrated). Often the paywalled tier.
6. **Add note / journal** — Optional longer-form reflection on top of mood entry.
7. **Paywall** — After 7-14 days of usage when insight density becomes meaningful. Annual plan default.
8. **Settings** — Reminders, integrations (HealthKit, calendar), export, privacy controls.

**Components needed:**
- Mood selector — large tap targets, color-coded
- Calendar heatmap — colored by mood
- Trend chart — line or bar over time
- Correlation insight card
- Quick check-in widget (Lock Screen / Home Screen)
- Insight paywall sheet

**Anti-patterns:**
- Excessive decoration — gets in the way of the 30-second check-in
- Forcing long journal entries — should be optional, not gated
- Gamification with leaderboards — wrong vibe entirely for mental health
- Insufficient privacy controls or unclear data handling — major trust killer for this category
- Notification spam — counterproductive for the actual user goal

**Cross-references:**
- DESIGN_SYSTEM.md → Mood selector, mood color tokens, heatmap, trend chart, insight card
- ARCHITECTURE.md → Local storage, HealthKit integration (optional), export formats, IAP, end-to-end encryption considerations

---

## Cross-references

This file is consumed by the `ux-ios` skill. Apple's HIG is the authoritative reference for navigation and platform conventions: https://developer.apple.com/design/human-interface-guidelines

For paywall implementation, the modern stack is RevenueCat or Superwall for analytics and A/B testing, with StoreKit 2 underneath.
