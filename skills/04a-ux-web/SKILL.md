---
name: ux-web
description: >
  Generate `UX.md` defining the UX architecture for a marketing website, landing page, or client/agency site — public-facing pages whose job is conversion. Covers page inventory, section order, conversion model per page, named components, and industry anti-patterns. Use whenever the user is planning a marketing site, landing page, agency portfolio, product launch page, waitlist, or any public web property measured in signups/leads/purchases. Triggers include "plan the landing page," "scaffold the marketing site," "what sections does this homepage need," "structure the agency site," "lay out the IA for this launch page," "plan the pricing page." Do NOT use for authenticated web applications (use `ux-app`) or native iOS apps (use `ux-ios`). For hybrid products, run this skill for the marketing surface and `ux-app` separately for the app surface.
---

# Plan UX (Web)

Generate a `UX.md` file: the UX backbone of a marketing website, landing page, or client/agency site. Defines what pages exist, what sections each contains in conversion order and why, the conversion model per page, industry-specific anti-patterns, and the named components each page requires (which feed directly into `DESIGN_SYSTEM.md`).

**Use this skill for:**
- Marketing sites (homepage + features + pricing + about)
- Single-page landing pages (waitlist, product launch, campaign)
- Agency/client/portfolio sites
- Pre-launch or coming-soon pages
- Sites whose primary job is *conversion* — getting a visitor to sign up, buy, book, or contact

**Do NOT use this skill for:**
- Authenticated web applications → use `ux-app`
- Native iOS apps → use `ux-ios`
- Hybrid products → use `ux-app` for the app surface, then this skill for any marketing landing pages, run separately

**UX vs UI separation:**
- `UX.md` = UX — information architecture, conversion flow, section ordering, what goes where and why
- `DESIGN_SYSTEM.md` = UI — how it looks once you know what it is (tokens, components, motion)

Generate `UX.md` first. `DESIGN_SYSTEM.md` references it — the component list in `DESIGN_SYSTEM.md` should derive from the components named here. If `docs/DESIGN_SYSTEM.md` already exists from `anchor-files`, cross-check after generation.

## Paths

Tracked artifacts use these default locations (override via `.arsenal/config.yaml` at the project root):

| Variable | Default | Holds |
|---|---|---|
| `paths.planning` | `planning/` | MARKET_RESEARCH.md, MVP_SPEC.md, FEATURES.md (or features/*.md), GTM_STRATEGY.md, REVENUE_MODEL.md, RESEARCH_PLAN.md |
| `paths.docs` | `docs/` | UX.md, DESIGN.md, DESIGN_SYSTEM.md, ARCHITECTURE.md, CONVENTIONS.md, TASKS.md |
| `paths.mockups` | `docs/mockups/` | Mockup files (PNG, HTML, TSX, Figma exports) |
| `paths.mockup_briefs` | `planning/mockup-briefs/` | Mockup briefs |

**Preflight (every run):** before reading or writing a tracked artifact, check for `.arsenal/config.yaml` at the project root. If present, parse `paths.*` and use those values; otherwise use defaults silently — do not prompt the user just to confirm defaults. File names (e.g. `MVP_SPEC.md`) are not configurable; only their wrapping directory is.

**Consuming an artifact from another skill:** if config (or defaults) point to a location where the expected artifact is missing, ask the user where to find it instead of failing.

## Workflow

### Step 1: Read existing planning context

Before asking the user anything, look for and read these files if they exist in the project:

- `planning/MVP_SPEC.md`
- `docs/ARCHITECTURE.md`
- `CLAUDE.md`
- Any other `planning/*` documents

Extract what's already known: product description, target audience, key pages, brand positioning. Don't re-ask what's already documented.

### Step 2: Discovery (only what's missing)

Only ask the user for what wasn't covered in the planning docs. Required information:

- **Product or service** — what's being marketed (one line)
- **Industry/category** — SaaS, fintech, agency, dev tool, e-commerce, etc.
- **Primary conversion goal** — free trial signup, demo booking, purchase, waitlist join, contact form, etc.
- **Pages in scope** — typical sets:
  - **Minimal:** single landing page only
  - **Standard:** Home + Pricing + About + Contact
  - **Full:** Home + Features (multiple) + Pricing + About + Blog + Contact + Legal
  - **Custom** — user specifies

For products with many pages, focus skeleton detail on the high-stakes ones (Home, Pricing, key feature pages). Secondary pages (About, Legal, Blog index) get one-line entries.

### Step 3: Look up industry reference

Open `references/skeletons.md` and resolve the industry match using this chain:

1. **Full section exists** — look for a `## <industry>` heading matching the project (e.g., `## SaaS (General)`, `## Fintech/Crypto`, `## Marketing Agency`). If found, use it as the structural template, customizing for this specific project.

2. **Compressed entry exists** — if no full section matches, check the "Appendix: Compressed industry index" table at the bottom of `skeletons.md`. Use the entry as a research seed for tier 4.

3. **No match → ask the user** — say: *"I don't have a reference skeleton for [industry]. Want me to (a) drop your own markdown reference under a new `## <industry>` heading in `references/skeletons.md`, (b) generate one from first principles using current best practices, or (c) do you have a reference site whose structure I should mirror?"*

4. **Research and generate** — if the user picks (b) or has no preference, research current best practices for that industry, use the closest existing full section in `skeletons.md` as a structural template, and generate a fresh skeleton in the established format.

5. **Promote to library** — when tier 3 or 4 produces a new skeleton, offer to add it as a new `## <industry>` section in `references/skeletons.md` so the library grows over time.

### Step 4: Generate UX.md

Using the reference as a starting point, generate the full `UX.md` customized for this project:

- Rename generic sections to match the actual product (e.g., "Feature grid" → "Integration showcase" for a dev tool)
- Add/remove sections based on the user's stated pages
- Fill in components-needed lists with names matching the product's vocabulary
- Customize anti-patterns with project-specific constraints
- Add cross-references to `DESIGN_SYSTEM.md` and `ARCHITECTURE.md` if those files exist
- Include the conversion sequence rationale (the why behind the section order)

**Output format per page:**

```markdown
## [Page name]

**Conversion model:** [demo-driven trust | story-driven emotion | comparison-driven decision | risk-removal trial | etc.]
**Primary CTA:** [exact CTA text or activation event]
**Conversion goal:** [what signals success on this page]

### Sections (in conversion order)
1. **[Section name]** — [one-line rationale: why this, why here, what work it does]
2. **[Section name]** — [rationale]
...

### Components needed
- [Component name] — [brief description]
- ...

### Anti-patterns
- [What not to do] — [why]
- ...

### Cross-references
- DESIGN_SYSTEM.md → [relevant components]
- ARCHITECTURE.md → [relevant data flows or integrations, if any]
```

### Step 5: Apply 2026 conversion sequence baseline

Regardless of industry, the underlying conversion sequence for most marketing pages follows:

1. **Stop the scroll** — headline + hero visual that names the specific outcome
2. **Earn trust** — proof (logos, ratings, named customers, named numbers)
3. **Explain the value** — benefits framed as outcomes, not features
4. **Remove doubt** — objections, FAQ, comparison, security/compliance
5. **Make the ask** — CTA with friction reducers (free trial, no card, money-back)

Industry templates customize this sequence but rarely abandon it. Use it as a sanity check — if a section doesn't serve one of these jobs, ask whether it earns its place.

### Step 6: Review & cross-check

After generating, tell the user:

- Where the file was created
- How many pages were skeletoned (full detail vs one-line)
- If `docs/DESIGN_SYSTEM.md` exists: list components named in `UX.md` that don't have a matching entry in `DESIGN_SYSTEM.md`, and offer to add them (or suggest running `design` to generate/update the design system)
- Offer to flesh out any section that needs more detail

## File placement

```
project-root/
└── docs/
    └── UX.md
```

If no `docs/` directory exists, ask the user where to put it. If `anchor-files` has been run, match its directory convention.

## Important guidelines

- **Be specific, not generic.** Name real sections, real components, real anti-patterns for this product. Don't output the reference template verbatim.
- **Customize the reference, don't copy it.** The reference is a starting point. The output should feel hand-crafted for this project.
- **High-stakes pages get full treatment. Secondary pages get one-liners.** Don't pad with boilerplate for an About page or footer.
- **Components are the bridge to DESIGN_SYSTEM.md.** Every component named here should eventually be defined there. Use names specific enough to design against ("pricing comparison table" not "table").
- **Anti-patterns are the highest-signal part.** Industry-specific "don't do this" guidance prevents expensive mistakes. Include at least 3-5 per high-stakes page.
- **Conversion sequence beats aesthetic preference.** The reason hero → proof → benefits → objections → CTA works isn't theory — it's user psychology (loss aversion, social validation, decision fatigue). Question deviations.
- **Cap `UX.md` at ~500 lines.** Long markdown bloats downstream context (`anchor-files`, `features`, design skills all read this) and becomes unscannable. Sweet spot: 100–400 lines. If the skeleton would exceed 500 lines, split into a parent `UX.md` (page inventory + IA + global anti-patterns) plus per-page sub-files in a `ux/` folder (e.g., `ux/home.md`, `ux/pricing.md`) and cross-link.
- **Cross-references keep the docs honest.** If `UX.md` names a component, `DESIGN_SYSTEM.md` should define it. If `UX.md` names an integration, `ARCHITECTURE.md` should document it.
- **2026 patterns worth knowing:**
  - Outcome-focused H1 (≤8 words) beats clever positioning
  - Specific named claims ("Used by 8 of the Fortune 50") beat unspecified scale ("Trusted by thousands")
  - Single CTA above the fold outperforms paired CTAs in most contexts
  - Loss-aversion framing ("Stop losing X hours") often beats gain framing ("Start saving X hours")
  - Mobile-first thumb-reach CTA placement is non-negotiable
