# DESIGN.md Template

> This template implements the **awesome-design-md** specification — a 9-section DESIGN.md format created by [VoltAgent](https://github.com/VoltAgent/awesome-design-md) (MIT licensed). The format spec, structure, and section conventions are theirs; this guide is original prose explaining how to apply the format inside the `design` skill.

Use this exact structure when writing the output `DESIGN.md`. Headings, ordering, and section names are fixed — they match the [getdesign.md / VoltAgent awesome-design-md](https://getdesign.md/) format that AI agents (Stitch, Claude Code, Cursor) expect.

**Read at least one example file in this same references folder before writing anything.** Each is a real, finished DESIGN.md from the awesome-design-md collection covering a different aesthetic territory. Use whichever is closest to the brand you're extracting as the North Star for voice, depth, and structure. Your output should feel like it could sit next to that file in the same repo.

**The three examples and when to read each:**

- **`example-claude.md`** — warm, editorial, quiet. Serif display type, parchment surfaces, single earthy accent. Read this as your **default** North Star; it's also the right primary reference for premium content brands, publishing, AI/research products, and anything that leans "quiet and considered."
- **`example-apple.md`** — cinematic, minimal, premium consumer. Massive sans-serif display at up to 301 px, pure monochrome sections alternating black and near-white, a single chromatic accent (Apple Blue `#0071e3`) reserved exclusively for interactive elements. Read this when the target brand is consumer hardware, luxury product, or any site that treats photography as the hero and retreats the UI around it.
- **`example-lamborghini.md`** — loud, luxury, dramatic, maximalist. Dark cinematic surfaces, aggressive display type, high-performance automotive voice. Read this when the target brand is automotive, luxury, bold/editorial, or any site that wants to feel kinetic and high-intensity rather than restrained.

You don't need to read all three. Pick the closest match — one, or two if the brand sits between archetypes. If you genuinely can't tell which archetype fits, read `example-claude.md` as the default; its structural discipline is a safe baseline.

---

## Structural completeness vs. content depth

Two different rules apply to a DESIGN.md, and confusing them is the most common failure mode.

**Structural completeness is non-negotiable.** Every DESIGN.md must contain all 9 sections and all subsections marked as required (Distinctive Components in Section 4, all four Section 5 subsections, Shadow Philosophy paragraph in Section 6, Iteration Guide in Section 9, etc.). Skipping a required subsection because the brand "doesn't have much there" is the failure mode — not undershooting a quantity range.

**Quantity guidance flexes with the brand.** Ranges like "12-25 colors", "6-9 do's/don'ts", "12-18 typography rows", "4-6 example prompts" are guidance, not floors. Match the brand's actual depth. A binary blue/white/black brand might genuinely have 9 colors; forcing it to 12 means inventing values. A brand with no shadows might have a one-line Section 6; padding it with a fabricated shadow scale makes the doc worse, not better.

**When a section is genuinely thin, write a one-line prose acknowledgment** — don't omit the section, and don't fabricate content to fill it.

```markdown
## 6. Depth & Elevation

Flat design — no shadow system used. Depth is communicated through borders
(`1px solid #f0eee6`) and color contrast between light and dark sections.
Do not introduce shadows when working with this design system.
```

That's a complete, valid Section 6 for a flat-design brand. The skipped element here is *content*, not *structure*.

---

## Top of file

```markdown
# Design System Inspired by <Brand>

<One-paragraph synthesis: industry, dominant aesthetic, what makes it distinctive. ~3 sentences. Example: "Apple's website is a masterclass in controlled drama — vast expanses of pure black and near-white serve as cinematic backdrops for products that are photographed as if they were sculptures in a gallery. The design philosophy is reductive to its core: every pixel exists in service of the product, and the interface itself retreats until it becomes invisible.">
```

No top-level metadata block, no "not an official" disclaimer in the file itself — the getdesign.md site adds those around the markdown. The DESIGN.md starts at Section 1.

---

## 1. Visual Theme & Atmosphere

**Format**: 2-4 prose paragraphs followed by a "Key Characteristics" bulleted list.

The prose should:
- Paragraph 1: name the dominant visual move (what you notice first)
- Paragraph 2: how typography supports the theme
- Paragraph 3: how color supports the theme
- (Optional) Paragraph 4: any defining structural quality (layout rhythm, photography style, motion language)

The Key Characteristics list should distill 6-10 single-line, opinionated takeaways that an agent could use as design constraints.

```markdown
## 1. Visual Theme & Atmosphere

<Paragraph 1: dominant aesthetic and design philosophy>

<Paragraph 2: typography's role>

<Paragraph 3: color story>

**Key Characteristics:**
- <Specific, distinctive trait — typography, color, layout, or motion>
- <Another>
- <Another>
- ...
```

---

## 2. Color Palette & Roles

**Format**: grouped subsections, NOT one flat table. Group by role family. Each color: bold name, hex/rgba in backticks, then a sentence on its role and why it exists in the system.

Common subsection groupings (use whichever apply to the brand — don't force groups that don't exist, don't omit groups that do):

```markdown
## 2. Color Palette & Roles

### Primary
- **<Color Name>** (`#hex`): <Where it's used + what role it plays in the visual hierarchy>
- ...

### Interactive
- **<Color Name>** (`#hex`): <CTAs, links, focus states. Note CSS variable name if you found one, e.g. `--sk-focus-color`>
- ...

### Text
- **<Color Name>** (`#hex` or `rgba(...)`): <On what background, at what weight>
- ...

### Surface & Dark Variants
- **<Color Name>** (`#hex`): <Card backgrounds, elevated surfaces>
- ...

### Button States
- **<Color Name>** (`#hex`): <Hover/active/disabled state>
- ...

### Shadows
- **<Shadow Name>** (`<full shadow value>`): <When applied>

### Gradient System
- <Either list the gradient tokens, or write a one-line acknowledgment: "Gradient-free — depth and richness come from surface tone interplay rather than gradients.">
```

Notes:
- Capture rgba opacity values exactly — don't round `rgba(0,0,0,0.48)` to `0.5`
- If you find CSS custom properties (e.g., `--sk-focus-color`), include them inline
- Aim for ~10-25 colors. Curate to the system the brand actually uses; don't pad to hit a number, and don't truncate a genuinely rich palette to fit one.

**Format check:**

❌ **Bad** (LLM default — flat table):

```
| Token  | Hex     | Role        |
|--------|---------|-------------|
| accent | #c96442 | Primary CTA |
```

✅ **Good** (grouped prose with named tokens):

```
**Terracotta Brand** (`#c96442`): The core brand color — a burnt orange-brown
used for primary CTA buttons, brand moments, and the signature accent.
Deliberately earthy and un-tech.
```

---

## 3. Typography Rules

**Format**: Font Family subsection, then Hierarchy table, then Principles subsection.

```markdown
## 3. Typography Rules

### Font Family
- **Display**: `<Font Name>`, with fallbacks: `<system stack>`
- **Body**: `<Font Name>`, with fallbacks: `<system stack>`
- <Note any optical sizing rules, e.g. "Display used at 20px+, Text below 19px">

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display Hero | <Font> | 56px (3.50rem) | 600 | 1.07 (tight) | -0.28px | <Use case> |
| Section Heading | <Font> | 40px (2.50rem) | 600 | 1.10 (tight) | normal | <Use case> |
| ...
| Body | <Font> | 17px (1.06rem) | 400 | 1.47 | -0.374px | Standard reading text |
| Caption | <Font> | 14px (0.88rem) | 400 | 1.29 (tight) | -0.224px | <Use case> |
| Micro | <Font> | 12px (0.75rem) | 400 | 1.33 | -0.12px | <Use case> |

### Principles
- <Opinionated rule about how this typography system actually works — e.g., "Optical sizing as philosophy: SF Pro automatically switches between Display and Text optical sizes...">
- <Another>
- <Another>
- <Another>
```

Notes:
- Include rem values when the source CSS uses rem; px-only is fine when the source is px-only. Don't fabricate unit conversions that aren't in the source.
- Annotate tight line-heights with `(tight)` and relaxed ones with `(relaxed)`
- Aim for 12-18 rows in the hierarchy table — capture the full scale, not just headlines. Brands with thinner type systems may have fewer; that's fine.
- Use `normal` for letter-spacing when the source doesn't apply tracking. The column should always exist even if most rows are `normal`.
- The Principles section is required and is where you call out the opinionated, distinctive rules (negative tracking universally, weight restraint, optical sizing, etc.)

---

## 4. Component Stylings

**Format**: each component named with a bold descriptor (not generic). Properties as bullets. End with a "Distinctive Components" subsection for site-signature patterns. **Distinctive Components is required** — even brands with mostly-standard components have *something* that makes them recognizable. If you genuinely can't find one, write "No distinctive components beyond standard buttons/cards" and explain why.

```markdown
## 4. Component Stylings

### Buttons

**<Site-specific button name, e.g. "Primary Blue (CTA)" or "Pill Link (Learn More / Shop)">**
- Background: `<value>`
- Text: `<color>`
- Padding: `<value>`
- Radius: `<value>`
- Border: `<value>`
- Font: `<font>, <size>, weight <n>`
- Hover: `<state change>`
- Active: `<state change>`
- Focus: `<state change>`
- Use: <When this variant appears>

**<Next button variant>**
- ...

### Cards & Containers
- Background: `<value>`
- Border: `<value or 'none'>`
- Radius: `<value>`
- Shadow: `<value or 'none'>`
- Content: <padding, alignment>
- Hover: <state>

### Navigation
- Background: `<value, including any backdrop-filter>`
- Height: `<value>`
- Text: `<color, size, weight>`
- Active state: `<treatment>`
- Mobile behavior: <how it collapses>

### Image Treatment
- <How images are handled — solid color fields, full-bleed, rounded containers, lifestyle vs product photography>

### Distinctive Components

**<Pattern unique to this site, e.g. "Product Hero Module">**
- <Description with specific values>

**<Another distinctive pattern>**
- ...
```

Notes:
- Use the site's actual button names where possible ("Pill Link", not "Tertiary Button")
- The Distinctive Components subsection is the most valuable — it captures patterns that make the site recognizable

---

## 5. Layout Principles

**Format**: Four required subsections — Spacing System, Grid & Container, Whitespace Philosophy, Border Radius Scale. All four must appear, even if one is brief.

```markdown
## 5. Layout Principles

### Spacing System
- Base unit: <value>
- Scale: <comma-separated values>
- <Notable characteristic about the scale — e.g., "dense at small sizes with granular 1px increments, then jumps in larger steps">

### Grid & Container
- Max content width: <value>
- Hero: <treatment>
- Product/content grids: <column behavior>
- <Other layout patterns>

### Whitespace Philosophy
- <Opinionated principle — e.g., "Cinematic breathing room: each section occupies a full viewport height...">
- <Another>
- <Another>

### Border Radius Scale
- Micro (<value>): <use>
- Standard (<value>): <use>
- Comfortable (<value>): <use>
- Large (<value>): <use>
- Full Pill (<value>): <use>
- Circle (50%): <use, if applicable>
```

---

## 6. Depth & Elevation

**Format**: Levels table, then Shadow Philosophy paragraph (required), then Decorative Depth subsection.

```markdown
## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, solid background | <Use> |
| <Named level> | <Full CSS treatment> | <Use> |
| Subtle Lift (Level 1) | `<full shadow value>` | <Use> |
| Focus (Accessibility) | `<focus ring value>` | Keyboard focus on all interactive elements |

**Shadow Philosophy**: <1-2 sentences on how this site uses shadow. Be opinionated — does it use one shadow style? Many? None? Why does the choice fit the brand?>

### Decorative Depth
- <How depth is communicated beyond shadows — color contrast, glass effects, layered photography, etc.>
- <Another>
```

If the site is genuinely flat, replace the table and Shadow Philosophy with the prose acknowledgment shown in "Structural completeness vs. content depth" at the top of this file. Don't fabricate a shadow system. The Decorative Depth subsection still applies and should explain how depth is communicated without shadows.

---

## 7. Do's and Don'ts

**Format**: two bulleted lists, each opinionated and site-specific.

```markdown
## 7. Do's and Don'ts

### Do
- <Specific, opinionated guideline rooted in the brand's distinctive choices>
- <Another>
- <Another>
- ... (aim for 6-10)

### Don't
- <Specific anti-pattern that would break the brand>
- <Another>
- <Another>
- ... (aim for 6-10)
```

Quality test: every Do/Don't should be specific enough that a generic site couldn't claim it. "Use consistent spacing" is useless. "Use 980px pill radius for CTA links — the signature Apple link shape" is the bar.

---

## 8. Responsive Behavior

**Format**: Breakpoints table (granular), then Touch Targets, Collapsing Strategy, Image Behavior subsections — all four required.

```markdown
## 8. Responsive Behavior

### Breakpoints

| Name | Width | Key Changes |
|------|-------|-------------|
| Small Mobile | <360px | Minimum supported, single column |
| Mobile | 360-480px | Standard mobile layout |
| Mobile Large | 480-640px | <Changes> |
| Tablet Small | 640-834px | <Changes> |
| Tablet | 834-1024px | <Changes> |
| Desktop Small | 1024-1070px | <Changes> |
| Desktop | 1070-1440px | <Changes> |
| Large Desktop | >1440px | <Changes> |

### Touch Targets
- <Minimum sizes for primary CTAs, nav, controls>

### Collapsing Strategy
- <How headlines scale: e.g. "56px Display → 40px → 28px on mobile, maintaining tight line-height proportionally">
- <How grids collapse>
- <How nav collapses>
- <What stays full-width / full-bleed>

### Image Behavior
- <Aspect ratio rules>
- <Cropping/scaling at breakpoints>
- <Lazy loading patterns>
```

Aim for 5-8 breakpoints in the table. Real sites have more granular breakpoint systems than the standard sm/md/lg/xl, but 5 is a reasonable floor when a brand only ships 5.

---

## 9. Agent Prompt Guide

**Format**: Quick Color Reference list, Example Component Prompts (4-6 of them), then a numbered Iteration Guide. **The Iteration Guide is required** — a DESIGN.md without it is a design doc; a DESIGN.md *with* it is a tool.

```markdown
## 9. Agent Prompt Guide

### Quick Color Reference
- Primary CTA: <name + hex>
- Page background (light): `<hex>`
- Page background (dark): `<hex>`
- Heading text (light): `<hex>`
- Heading text (dark): `<hex>`
- Body text: `<value>` on light, `<value>` on dark
- Link (light bg): `<hex>`
- Link (dark bg): `<hex>`
- Focus ring: `<hex>`
- Card shadow: `<full value>`

### Example Component Prompts
- "<Full prompt for a hero section, with every spec inline — bg color, headline font/size/weight/tracking/line-height, subtitle specs, CTA specs>"
- "<Full prompt for a product/feature card with every spec inline>"
- "<Full prompt for the navigation pattern>"
- "<Full prompt for a distinctive layout pattern unique to this site>"
- "<Full prompt for a signature component like a 'Learn more' link or pill CTA>"

### Iteration Guide
1. <First non-negotiable rule — e.g., "Every interactive element gets <accent color> — no other accent colors">
2. <Second rule — typically a section/layout pattern>
3. <Third rule — typography behavior>
4. <Fourth rule — distinctive treatment>
5. <Fifth rule — what makes the brand recognizable>
6. <Sixth rule — what to avoid>
7. <Seventh rule — depth/elevation>
8. <Eighth rule — a signature shape or component>
```

The Iteration Guide is the most valuable part for agents. Every numbered point should be a hard, unambiguous rule that an agent can follow without judgment calls.

---

## Field-level guidance

### What "good" looks like
A reviewer should be able to hand this DESIGN.md to a coding agent and get UI that's recognizably <Brand>-derivative — not generic. Test by reading just Section 9 (Agent Prompt Guide) and asking: "Could I build this without seeing the source?"

### What "bad" looks like
- Section 1 as bullets only, no prose (the prose is where the design philosophy actually lives)
- Section 2 as one flat table instead of grouped subsections
- Generic component names ("Primary Button") instead of site-specific ones ("Pill Link", "Media Control")
- Skipping required subsections (Distinctive Components, Shadow Philosophy, Iteration Guide, the four Section 5 subsections, the four Section 8 subsections) because the brand "doesn't have much there"
- Padding thin sections with fabricated content to hit quantity ranges
- Only 3-4 breakpoints when the site clearly has more
- Section 9 missing the Iteration Guide
- Vague mood words without supporting tokens
- Inventing values you didn't actually find in the source
- Inventing rem conversions when the source uses px-only

### How to handle uncertainty
If you can't determine a value with confidence, mark it explicitly:

```markdown
- Hover state: **[inferred]** `#0059C7` (10% darker variant — couldn't isolate hover CSS rule)
```

Better to flag inference than ship a confident guess.

### Length expectations
A complete DESIGN.md typically runs 200-400 lines depending on brand depth. The Claude example is ~290 lines; thinner brands run shorter. If you're writing under 150 you're probably skipping required subsections. If you're writing over 500 you're padding.