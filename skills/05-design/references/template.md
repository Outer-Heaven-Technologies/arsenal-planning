# DESIGN.md Template

> This template implements the **Google DESIGN.md format** (Apache 2.0 — [google-labs-code/design.md](https://github.com/google-labs-code/design.md)). The format pairs a YAML token block with markdown prose so coding agents can read the tokens directly while humans read the rationale. Last synced against spec version: **alpha (2026-05-13)**.

Use this exact structure when writing the output `DESIGN.md`. Token schema, section names, ordering — all fixed. The file is validated by `npx @google/design.md@latest lint`; outputs that fail lint do not ship.

---

## File anatomy

Every DESIGN.md is two layers stacked top-to-bottom:

1. **YAML front matter** between two `---` fences — machine-readable design tokens (the normative values).
2. **Markdown body** — `##` sections of human-readable rationale (the *why* and the *how to apply*).

```md
---
version: alpha
name: <Design system name>
description: <Optional one-line summary>
colors:
  <name>: "<#rrggbb>"
typography:
  <name>:
    fontFamily: <font name>
    fontSize: <value+unit>
    fontWeight: <number>
    lineHeight: <value+unit or unitless multiplier>
    letterSpacing: <value+unit>
rounded:
  <scale>: <value+unit>
spacing:
  <scale>: <value+unit or unitless number>
components:
  <component-name>:
    <property>: <value or "{token.path}">
---

## Overview
...
## Colors
...
```

The YAML between `---` fences is parsed as the token source of truth. Prose may use descriptive color names (e.g. *"Boston Clay"*); the token name (e.g. `tertiary`) is what agents resolve to.

---

## Top-of-file conventions

The file may begin with an optional `# <Project name>` H1 (not parsed as a section). Sections themselves are `## ` (H2). Do not use H1 inside the body.

---

## Token schema reference

### Primitive types

| Type | Format | Example |
|---|---|---|
| **Color** | `#` + hex (sRGB) | `"#1A1C1E"` |
| **Dimension** | number + unit `px` / `em` / `rem` | `48px`, `1.5rem`, `-0.02em` |
| **Token reference** | `{path.to.token}` | `{colors.primary}` |
| **Typography** | object — fields below | (see below) |

### Typography fields

| Field | Type | Notes |
|---|---|---|
| `fontFamily` | string | e.g. `"Public Sans"` |
| `fontSize` | Dimension | e.g. `48px` |
| `fontWeight` | number | `400` / `500` / `600` / `700` etc. May be quoted or bare in YAML; both equivalent |
| `lineHeight` | Dimension or unitless number | `24px` or `1.5` (unitless = multiplier of `fontSize`) |
| `letterSpacing` | Dimension | e.g. `-0.02em` |
| `fontFeature` | string | maps to CSS `font-feature-settings` |
| `fontVariation` | string | maps to CSS `font-variation-settings` |

### Token reference rules

- Most groups: refs must point to a **primitive** value (e.g. `{colors.primary}`), not a group (`{colors}`).
- Inside `components`, refs to **composite values** are allowed (e.g. `{typography.label-md}`).
- Component property tokens accept: `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width`.

---

## Recommended token names (non-normative but conventional)

Following these makes the file portable across agents and tools. Use these when they fit; invent new names when the brand warrants.

- **Colors:** `primary`, `secondary`, `tertiary`, `neutral`, `surface`, `on-surface`, `error`
- **Typography:** `headline-display`, `headline-lg`, `headline-md`, `body-lg`, `body-md`, `body-sm`, `label-lg`, `label-md`, `label-sm`
- **Rounded:** `none`, `sm`, `md`, `lg`, `xl`, `full`
- **Spacing:** `xs`, `sm`, `md`, `lg`, `xl`, `base`, `gutter`, `margin`

---

## Section order (fixed)

Sections must appear in this order when present. Any may be omitted if not relevant. Aliases in parentheses are accepted variants.

| # | Section | Aliases |
|---|---|---|
| 1 | Overview | Brand & Style |
| 2 | Colors | |
| 3 | Typography | |
| 4 | Layout | Layout & Spacing |
| 5 | Elevation & Depth | Elevation |
| 6 | Shapes | |
| 7 | Components | |
| 8 | Do's and Don'ts | |

Unknown sections (e.g. `## Motion`, `## Iconography`) are preserved by spec-compliant consumers but are non-normative.

---

## Structural completeness vs. content depth

Two rules apply, and confusing them is the most common failure mode.

**Structural completeness is non-negotiable.** Every DESIGN.md that ships through the `design` skill must include sections 1–3 (Overview, Colors, Typography) and at least the `colors.primary` and one typography level token. Sections 4–8 are spec-optional but become required as soon as the brand has anything to say about them — a brand with a flat aesthetic still gets a Section 5 explaining *that fact*; it doesn't get a missing Section 5.

**Quantity guidance flexes with the brand.** A binary blue/white/black brand might have 6 colors; a maximalist editorial brand might have 25. Match the brand's actual depth — don't pad to hit a number, don't truncate to look tight.

**When a section is genuinely thin, write a one-paragraph prose acknowledgment** rather than omitting the section.

```markdown
## Elevation & Depth

Flat design — no shadow system. Depth is conveyed through 1px borders
(`{colors.neutral}`) and surface-color contrast between Sections 2 and
4 tones. Do not introduce shadows when working with this design system.
```

That's a valid Section 5 for a flat brand.

---

## Section-by-section guidance

### 1. Overview *(also: "Brand & Style")*

Holistic description of look and feel. **2–4 prose paragraphs.** Defines brand personality, target audience, the emotional response the UI should evoke. Often the longest prose section.

The opening paragraph should name the dominant visual move — what a user notices first. Subsequent paragraphs handle typography's role, color's role, and any defining structural quality (layout rhythm, photography style, motion language).

Close with **6–10 single-line Key Characteristics** as a bulleted list that an agent could read as design constraints.

```markdown
## Overview

<P1: dominant aesthetic and design philosophy>

<P2: typography's role>

<P3: color story>

**Key Characteristics:**
- <Specific, distinctive trait>
- <Another>
- ...
```

### 2. Colors

Required. **Prose first** describing the palette philosophy and the role of each palette (primary, secondary, tertiary, neutral, accents). Then the YAML token block.

Most design systems need at least `primary`, plus 1–4 supporting palettes and a neutral set. Provide as many colors as the brand actually has — typically 6–25.

```markdown
## Colors

The palette is rooted in high-contrast neutrals and a single, evocative
accent color.

- **Primary (#1A1C1E):** A deep ink for headlines and core text — maximum readability, sense of permanence.
- **Secondary (#6C7278):** Sophisticated slate for borders, captions, metadata.
- **Tertiary (#B8422E):** Boston Clay — the sole driver for interaction.
- **Neutral (#F7F5F2):** Warm limestone foundation for page backgrounds.
```

Token YAML for this section lives in the front matter as the `colors:` map. Use semantic names (`primary`, `secondary`, `tertiary`, `neutral`, `surface`, `on-surface`, `error`) or numeric scales (`primary-10`, `primary-50`, `primary-90`) — both are valid. Pick the convention that fits the system.

### 3. Typography

Required. **Prose first** describing fonts, weights, and the role of each level. Then YAML.

Most design systems define **9–15 typography levels** across categories like `headline`, `display`, `body`, `label`, `caption`, each with size variants (`sm`, `md`, `lg`).

```markdown
## Typography

The typography strategy leverages two distinct weights of **Public Sans**
for the narrative and **Space Grotesk** for technical data.

- **Headlines:** Public Sans Semi-Bold — institutional and trustworthy.
- **Body:** Public Sans Regular at 16px — contemporary professionalism.
- **Labels:** Space Grotesk for timestamps and metadata; uppercase with
  generous letter spacing — evokes digital-stopwatch precision.
```

YAML schema reminder:

```yaml
typography:
  headline-lg:
    fontFamily: Public Sans
    fontSize: 48px
    fontWeight: 600
    lineHeight: 1.1
    letterSpacing: -0.02em
  body-md:
    fontFamily: Public Sans
    fontSize: 16px
    fontWeight: 400
    lineHeight: 1.6
  label-caps:
    fontFamily: Space Grotesk
    fontSize: 12px
    fontWeight: 500
    lineHeight: 1
    letterSpacing: 0.1em
```

### 4. Layout *(also: "Layout & Spacing")*

Describes the layout model (grid vs. flex vs. dynamic margins) and the spacing scale. Typically **1–2 prose paragraphs** plus a token block.

Spacing tokens go under the `spacing:` map in YAML — usually 5–8 scale steps plus named values for `gutter` and `margin` if a grid is in use.

### 5. Elevation & Depth *(also: "Elevation")*

How visual hierarchy is conveyed. **1–2 paragraphs.** If shadows are used, define their spread/blur/color philosophy. If the system is flat, say so explicitly and name the alternative depth signals (borders, color contrast, surface tonality).

This section has no required token block — but if the system uses elevation tokens, they may be defined here as a non-normative extension.

### 6. Shapes

Describes how visual elements are shaped — corner radii, lozenge vs. capsule vs. rectangle conventions. **1–2 paragraphs** plus `rounded:` token block in YAML.

```yaml
rounded:
  none: 0px
  sm: 4px
  md: 8px
  lg: 12px
  full: 9999px
```

### 7. Components

Style guidance for atomic components — buttons, chips, lists, tooltips, checkboxes, radios, inputs, etc. Cover at least primary/secondary/tertiary button variants plus inputs.

**Prose per component**, followed by the YAML `components:` map. Each component is a sub-map of property tokens.

```yaml
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.neutral}"
    rounded: "{rounded.md}"
    padding: 12px
    typography: "{typography.label-md}"
  button-primary-hover:
    backgroundColor: "{colors.primary-70}"
  button-secondary:
    backgroundColor: transparent
    textColor: "{colors.primary}"
    rounded: "{rounded.md}"
    padding: 12px
```

**Variants** for interaction states (hover, pressed, active, disabled) use sibling token names: `button-primary` + `button-primary-hover` + `button-primary-active`, etc.

Component property tokens accepted by the spec: `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width`.

### 8. Do's and Don'ts

Practical guardrails. **6–10 bulleted rules** — concrete enough that an agent can check its work against them.

```markdown
## Do's and Don'ts

- Do use the primary color for the single most important action per screen
- Do maintain WCAG AA contrast (4.5:1 for normal text)
- Don't mix rounded and sharp corners in the same view
- Don't use more than two font weights on a single screen
- Don't introduce gradients — this brand is flat by design
- Do reserve the tertiary color for interaction; never decoration
```

---

## Validation

Every DESIGN.md produced by the `design` skill is linted before being promoted to the library:

```bash
npx @google/design.md@latest lint <path-to-DESIGN.md>
```

Errors block promotion. Warnings (e.g. WCAG contrast, soft-deprecated patterns) are surfaced for user review.

If lint fails with version-mismatch errors (e.g. our template uses `version: alpha` but the spec has moved on), update this `template.md` and `example-claude.md` against the upstream repo (`https://github.com/google-labs-code/design.md`) and re-emit.

---

## Worked-example pointer

Read `references/example-claude.md` alongside this template before writing. The example demonstrates appropriate section depth, token granularity, and prose voice. Reproduce the *care* and *specificity*, not the aesthetic — different brands warrant very different aesthetic registers.
