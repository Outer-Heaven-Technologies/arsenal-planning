<!--
Reference example: Claude DESIGN.md in Google DESIGN.md format (Apache 2.0 —
https://github.com/google-labs-code/design.md).

Use this file as the North Star for token granularity, section depth, and prose
voice when writing new DESIGN.md outputs. What to notice:

- The YAML front matter is the normative token source — every value an agent
  needs is here, machine-readable.
- The markdown prose explains *why* those values exist and *how* to apply them.
  The Overview section is three paragraphs of brand storytelling before any
  bullet list.
- Token names lean on the spec's conventional names (primary, secondary,
  surface) but also include brand-specific descriptive names (terracotta,
  parchment, warm-sand) as additional tokens — the same value can have
  multiple keys.
- Every color in prose has an evocative name AND a {token.reference}.
- Components define interaction-state variants as sibling tokens
  (button-primary + button-primary-hover).
- Do's/Don'ts are brand-specific — "Don't use cool blue-grays anywhere" is
  unique to Claude; "Use consistent spacing" would be useless boilerplate.

This file is a valid DESIGN.md — lint with:
  npx @google/design.md@latest lint example-claude.md
-->

---
version: alpha
name: Claude
description: Anthropic Claude — a literary salon reimagined as a product page. Warm, unhurried, quietly intellectual. Parchment-toned canvas, custom serif headlines, organic terracotta accents.

colors:
  # Semantic palette (Google-recommended names)
  primary: "#c96442"            # Terracotta Brand — earthy CTAs
  secondary: "#e8e6dc"          # Warm Sand — workhorse interactive
  tertiary: "#5e5d59"           # Olive Gray — supporting text
  neutral: "#f5f4ed"            # Parchment — page background
  surface: "#faf9f5"            # Ivory — elevated containers
  on-surface: "#141413"         # Anthropic Near Black — primary text
  error: "#b53333"              # Error Crimson — warm-toned alarm

  # Brand-specific descriptive tokens (additional aliases)
  parchment: "#f5f4ed"
  ivory: "#faf9f5"
  white: "#ffffff"
  warm-sand: "#e8e6dc"
  near-black: "#141413"
  deep-dark: "#141413"
  dark-surface: "#30302e"
  terracotta: "#c96442"
  coral: "#d97757"
  charcoal-warm: "#4d4c48"
  olive-gray: "#5e5d59"
  stone-gray: "#87867f"
  dark-warm: "#3d3d3a"
  warm-silver: "#b0aea5"

  # Borders & rings
  border-cream: "#f0eee6"
  border-warm: "#e8e6dc"
  border-dark: "#30302e"
  ring-warm: "#d1cfc5"
  ring-deep: "#c2c0b6"

  # Reserved cool color (accessibility only)
  focus-blue: "#3898ec"

typography:
  display:
    fontFamily: Anthropic Serif
    fontSize: 64px
    fontWeight: 500
    lineHeight: 1.10
  headline-lg:
    fontFamily: Anthropic Serif
    fontSize: 52px
    fontWeight: 500
    lineHeight: 1.20
  headline-md:
    fontFamily: Anthropic Serif
    fontSize: 36px
    fontWeight: 500
    lineHeight: 1.30
  headline-sm:
    fontFamily: Anthropic Serif
    fontSize: 32px
    fontWeight: 500
    lineHeight: 1.10
  title:
    fontFamily: Anthropic Serif
    fontSize: 25px
    fontWeight: 500
    lineHeight: 1.20
  feature:
    fontFamily: Anthropic Serif
    fontSize: 21px
    fontWeight: 500
    lineHeight: 1.20
  body-serif:
    fontFamily: Anthropic Serif
    fontSize: 17px
    fontWeight: 400
    lineHeight: 1.60
  body-lg:
    fontFamily: Anthropic Sans
    fontSize: 20px
    fontWeight: 400
    lineHeight: 1.60
  body-md:
    fontFamily: Anthropic Sans
    fontSize: 17px
    fontWeight: 400
    lineHeight: 1.60
  body-sm:
    fontFamily: Anthropic Sans
    fontSize: 16px
    fontWeight: 400
    lineHeight: 1.50
  caption:
    fontFamily: Anthropic Sans
    fontSize: 14px
    fontWeight: 400
    lineHeight: 1.43
  label:
    fontFamily: Anthropic Sans
    fontSize: 12px
    fontWeight: 500
    lineHeight: 1.25
    letterSpacing: 0.12px
  overline:
    fontFamily: Anthropic Sans
    fontSize: 10px
    fontWeight: 400
    lineHeight: 1.60
    letterSpacing: 0.5px
  code:
    fontFamily: Anthropic Mono
    fontSize: 15px
    fontWeight: 400
    lineHeight: 1.60
    letterSpacing: -0.32px

rounded:
  none: 0px
  sharp: 4px
  subtle: 6px
  comfortable: 8px
  generous: 12px
  very: 16px
  highly: 24px
  max: 32px

spacing:
  xs: 4px
  sm: 8px
  md: 16px
  lg: 24px
  xl: 32px
  2xl: 80px
  base: 8px
  gutter: 24px

components:
  button-primary:
    backgroundColor: "{colors.terracotta}"
    textColor: "{colors.ivory}"
    rounded: "{rounded.comfortable}"
    padding: 8px 16px
    typography: "{typography.body-sm}"
  button-primary-hover:
    backgroundColor: "{colors.coral}"
  button-secondary:
    backgroundColor: "{colors.warm-sand}"
    textColor: "{colors.charcoal-warm}"
    rounded: "{rounded.comfortable}"
    padding: 8px 12px
    typography: "{typography.body-sm}"
  button-tertiary:
    backgroundColor: "{colors.white}"
    textColor: "{colors.near-black}"
    rounded: "{rounded.generous}"
    padding: 8px 16px
  button-dark:
    backgroundColor: "{colors.dark-surface}"
    textColor: "{colors.ivory}"
    rounded: "{rounded.comfortable}"
    padding: 8px 12px
  button-dark-primary:
    backgroundColor: "{colors.near-black}"
    textColor: "{colors.warm-silver}"
    rounded: "{rounded.generous}"
    padding: 9.6px 16.8px
  card:
    backgroundColor: "{colors.ivory}"
    textColor: "{colors.on-surface}"
    rounded: "{rounded.comfortable}"
    padding: 24px
  card-featured:
    backgroundColor: "{colors.ivory}"
    rounded: "{rounded.very}"
    padding: 32px
  card-dark:
    backgroundColor: "{colors.dark-surface}"
    textColor: "{colors.warm-silver}"
    rounded: "{rounded.comfortable}"
    padding: 24px
  input:
    backgroundColor: "{colors.white}"
    textColor: "{colors.near-black}"
    rounded: "{rounded.generous}"
    padding: 1.6px 12px
    typography: "{typography.body-md}"
  input-focus:
    backgroundColor: "{colors.white}"
---

# Design System Inspired by Claude (Anthropic)


## Overview

Claude's interface is a literary salon reimagined as a product page — warm, unhurried, and quietly intellectual. The entire experience is built on a parchment-toned canvas (`{colors.parchment}` / `#f5f4ed`) that deliberately evokes the feeling of high-quality paper rather than a digital surface. Where most AI product pages lean into cold, futuristic aesthetics, Claude's design radiates human warmth — as if the AI itself has good taste in interior design.

The signature move is the custom Anthropic Serif typeface — a medium-weight serif with generous proportions that gives every headline the gravitas of a book title. Combined with organic, hand-drawn-feeling illustrations in terracotta (`{colors.terracotta}`), black, and muted green, the visual language says *"thoughtful companion"* rather than *"powerful tool."* The serif headlines breathe at tight-but-comfortable line-heights (1.10–1.30), creating a cadence that feels more like reading an essay than scanning a product page.

What makes Claude's design truly distinctive is its **warm neutral palette**. Every gray has a yellow-brown undertone (`{colors.olive-gray}`, `{colors.stone-gray}`, `{colors.charcoal-warm}`) — there are no cool blue-grays anywhere. Borders are cream-tinted, shadows use warm transparent blacks, and even the darkest surfaces (`{colors.near-black}`, `{colors.dark-surface}`) carry a barely perceptible olive warmth. This chromatic consistency creates a space that feels lived-in and trustworthy.

**Key Characteristics:**

- Warm parchment canvas evoking premium paper, not screens
- Custom Anthropic type family: Serif for headlines, Sans for UI, Mono for code
- Terracotta brand accent — warm, earthy, deliberately un-tech
- Exclusively warm-toned neutrals — every gray has a yellow-brown undertone
- Organic, editorial illustrations replacing typical tech iconography
- Ring-based shadow system creating border-like depth without visible borders
- Magazine-like pacing with generous section spacing and serif-driven hierarchy

## Colors

The palette is rooted in **exclusively warm tones** and a single chromatic accent. Every gray carries a yellow-brown undertone; every "black" is actually a warm near-black. The only cool color (`{colors.focus-blue}`) is reserved for input focus rings and exists solely for accessibility.

**Brand & accent:**

- **Terracotta Brand (`{colors.terracotta}`):** The core brand color — a burnt orange-brown used exclusively for primary CTAs and the highest-signal brand moments. Earthy, un-tech.
- **Coral Accent (`{colors.coral}`):** Lighter warmer variant of terracotta — used for text accents, links on dark surfaces, and secondary emphasis.

**Surfaces & backgrounds:**

- **Parchment (`{colors.parchment}`):** Primary page background — a warm cream with a yellow-green tint that feels like aged paper. The emotional foundation of the entire design.
- **Ivory (`{colors.ivory}`):** Lightest surface — used for cards and elevated containers on Parchment. Barely distinguishable but creates subtle layering.
- **Warm Sand (`{colors.warm-sand}`):** Workhorse interactive surfaces — a noticeably warm light gray for secondary buttons and prominent inputs.
- **Dark Surface (`{colors.dark-surface}`):** Dark-theme containers, nav borders, and elevated dark elements — warm charcoal.
- **Near Black (`{colors.near-black}`):** Dark-theme page background and primary text — not pure black, warm-tinted.

**Text & neutrals:**

- **Charcoal Warm (`{colors.charcoal-warm}`):** Button text on light warm surfaces — the go-to dark-on-light text.
- **Olive Gray (`{colors.olive-gray}`):** Secondary body text — a distinctly warm medium-dark gray.
- **Stone Gray (`{colors.stone-gray}`):** Tertiary text, footnotes, de-emphasized metadata.
- **Warm Silver (`{colors.warm-silver}`):** Text on dark surfaces — warm parchment-tinted light gray.

**Semantic:**

- **Error Crimson (`{colors.error}`):** Deep, warm red for error states — serious without being alarming.
- **Focus Blue (`{colors.focus-blue}`):** Standard accessibility blue for input focus rings — the *only* cool color in the entire system, used purely for accessibility compliance.

The system is **gradient-free** in the traditional sense. Depth and visual richness come from the interplay of warm surface tones, organic illustrations, and light/dark section alternation. The warm palette itself creates a perceived "gradient" as the eye moves through cream → sand → stone → charcoal → black sections.

## Typography

The typography strategy leverages a custom three-family system, with each font assigned a distinct functional role:

- **Anthropic Serif** for all headline content at weight 500 — gives every heading the gravitas of a published title.
- **Anthropic Sans** for all functional UI text (buttons, labels, navigation) with quiet efficiency.
- **Anthropic Mono** strictly for code — never for non-code content.

**Principles:**

- **Single weight for serifs.** All Anthropic Serif headings use weight 500 — no bold, no light. The single-weight consistency creates a uniform "voice" across all heading sizes.
- **Relaxed body line-height (1.60).** Significantly more generous than typical tech sites (1.4–1.5). Creates a reading experience closer to a book than a dashboard.
- **Tight-but-not-compressed headings (1.10–1.30).** The serif letterforms need breathing room sans-serif fonts don't.
- **Micro letter-spacing on small labels.** 12px-and-below sans text uses deliberate letter-spacing (0.12px–0.5px) to maintain readability at tiny sizes.

For external implementations, **Georgia** serves as the serif substitute and **system-ui / Inter** as the sans substitute.

## Layout

Claude follows a **container-based** layout with a max width of approximately 1200px, centered. Hero sections use centered editorial layouts; feature sections alternate between single-column and 2–3 column card grids. Full-width dark sections occasionally break the container for emphasis.

The spacing system is built on an **8px base unit** with a mixed scale (`{spacing.xs}` = 4px through `{spacing.2xl}` = 80px). Section vertical spacing is generous — estimated 80–120px between major sections — which supports the "editorial pacing" goal of the design. Card internal padding sits at 24–32px to emphasize the soft, approachable nature of the brand.

**Whitespace philosophy:** each section breathes like a magazine spread. Serif headings establish a literary cadence that demands more whitespace than sans-serif designs.

## Elevation & Depth

Claude communicates depth through **warm-toned ring shadows** rather than traditional drop shadows. The signature `0px 0px 0px 1px` ring pattern creates a border-like halo softer than an actual border — a shadow pretending to be a border, or a border that's technically a shadow.

| Level | Treatment | Use |
|---|---|---|
| Flat | No shadow, no border | Parchment backgrounds, inline text |
| Contained | `1px solid {colors.border-cream}` (light) or `1px solid {colors.border-dark}` (dark) | Standard cards, section dividers |
| Ring | `0px 0px 0px 1px` ring shadows using `{colors.ring-warm}` or `{colors.ring-deep}` | Interactive cards, button hover states |
| Whisper | `rgba(0,0,0,0.05) 0px 4px 24px` | Elevated feature cards, product screenshots |
| Inset | `inset 0px 0px 0px 1px` at 15% opacity | Active / pressed button states |

When drop shadows do appear, they're extremely soft (0.05 opacity, 24px blur) — barely visible lifts that suggest floating rather than casting. Heavy drop shadows are forbidden by the brand.

The most dramatic depth effect comes from **alternating between Parchment and Near Black sections** — entire sections shift elevation by changing the ambient light level, like chapters in a book.

## Shapes

Border-radius follows a **named-scale** system rather than a strict numeric progression. The brand reserves sharp corners (`{rounded.sharp}` = 4px) for minimal inline elements and uses progressively softer radii for larger surfaces.

```yaml
rounded:
  sharp: 4px         # minimal inline elements
  subtle: 6px        # small secondary buttons
  comfortable: 8px   # standard buttons + cards (most common)
  generous: 12px     # primary buttons, inputs, nav
  very: 16px         # featured containers, video players
  highly: 24px       # tag-like elements
  max: 32px          # hero containers, embedded media
```

Softness is core to the identity — sharp corners (< 6px) on buttons or cards are a brand violation.

## Components

### Buttons

Five named variants. The **Warm Sand secondary** is the workhorse; **Brand Terracotta** is the singular primary CTA color across the site.

- **`{components.button-primary}` (Brand Terracotta):** The only button with chromatic color — reserved for primary CTAs and the highest-signal brand moments.
- **`{components.button-secondary}` (Warm Sand):** Warm, unassuming, clearly interactive. The default light-surface button.
- **`{components.button-tertiary}` (White Surface):** Clean, elevated button for use against Parchment.
- **`{components.button-dark}` (Dark Charcoal):** Inverted variant for dark-on-light emphasis.
- **`{components.button-dark-primary}`:** Dark-theme version with Warm Silver text and a thin Dark Surface border.

Hover states shift the background subtly (often via the `-hover` variant token). Button padding is often **asymmetric** (e.g. `0px 12px 0px 8px`) reflecting an icon-first layout.

### Cards & containers

Cards use **Ivory** on light surfaces and **Dark Surface** on dark, with **Border Cream** containment lines. Standard cards use `{rounded.comfortable}` (8px); featured cards step up to `{rounded.very}` (16px); hero containers reach `{rounded.max}` (32px). Whisper shadows (`{colors}` rgba 0.05 opacity, 24px blur) lift elevated content.

### Inputs

Text inputs use **Pure White** backgrounds with **Near Black** text on light surfaces. Padding is very compact vertically (1.6px) but generous horizontally (12px). Focus state uses **Focus Blue** for the border ring — the only cool color moment in the entire system, used solely for accessibility.

### Distinctive components

- **Model Comparison Cards:** Opus / Sonnet / Haiku presented in a clean 3-column grid with Border Warm separators.
- **Organic Illustrations:** Hand-drawn-feeling vector illustrations in terracotta, black, and muted green — abstract and conceptual rather than literal product diagrams. The primary visual personality; no other AI company uses this style.
- **Dark/Light Section Alternation:** The page alternates between Parchment light and Near Black dark sections, creating a reading rhythm like chapters in a book.

## Do's and Don'ts

**Do:**

- Use Parchment (`{colors.parchment}`) as the primary light background — the warm cream tone *is* the Claude personality
- Use Anthropic Serif at weight 500 for all headlines — single-weight consistency is intentional
- Use Terracotta (`{colors.terracotta}`) only for primary CTAs and the highest-signal brand moments — never decoration
- Keep all neutrals warm-toned — every gray should have a yellow-brown undertone
- Use ring shadows (`0px 0px 0px 1px`) for interactive element states instead of drop shadows
- Maintain the editorial serif/sans hierarchy — serif for content headlines, sans for UI
- Use generous body line-height (1.60) for a literary reading experience
- Alternate light/dark sections to create chapter-like page rhythm

**Don't:**

- Don't use cool blue-grays anywhere — the palette is exclusively warm-toned
- Don't use bold (700+) weight on Anthropic Serif — weight 500 is the ceiling for serifs
- Don't introduce saturated colors beyond Terracotta — the palette is deliberately muted
- Don't use sharp corners (< 6px radius) on buttons or cards — softness is core to the identity
- Don't apply heavy drop shadows — depth comes from ring shadows and section alternation
- Don't use pure white (`#ffffff`) as a page background — Parchment or Ivory are always warmer
- Don't use geometric/tech-style illustrations — Claude's illustrations are organic and hand-drawn-feeling
- Don't reduce body line-height below 1.40 — generous spacing supports the editorial personality
- Don't mix sans-serif into headlines — the serif/sans split is the typographic identity
