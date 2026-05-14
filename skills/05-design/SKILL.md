---
name: design
description: Produces a `DESIGN.md` brand spec via three paths — fetch from the getdesign.md catalog (verbatim), extract faithfully from URLs, or invent from written direction with optional refs. Maintains a personal library at `~/.claude/design-md-library/`. Auto-fires on "get me Shopify", "make a DESIGN.md like [Brand]", "extract design from [url]", "inspired by X", "design that feels like X meets Y". Use when the user wants design tokens, a brand spec, or a DESIGN.md for a known brand or original aesthetic.
---

# Plan Design

Produces a `DESIGN.md` file in the **[Google DESIGN.md format](https://github.com/google-labs-code/design.md)** (Apache 2.0) — YAML front matter holding machine-readable design tokens, plus a markdown body of 8 structured sections. Three source paths, one personal library, one lint gate.

**Output format is fixed.** Every DESIGN.md this skill ships is valid Google DESIGN.md — verified by `npx @google/design.md@latest lint` before promotion to the library. The lint gate is mandatory, not optional. Files that fail lint do not get saved.

**Core principle — faithful for sources, original for inspiration.** Path A fetches a known brand from the getdesign.md catalog and **transforms** it from VoltAgent's 9-section format into Google's 8-section + YAML format (the catalog's content is canonical brand truth; we normalize the shape). Path B produces faithful new writing extracted from real URLs. Path C produces original new writing influenced by refs but not reproducing them.

**The B-vs-C line.** Path B reproduces existing designs faithfully (single or multi-URL — refs ARE the source). Path C creates new designs using refs (URLs, words, archetypes) as inspiration — refs INFORM the source, they don't become it. If the user provides any written direction, or uses blend language ("X meets Y", "inspired by X but Z"), it's C. Pure URLs with faithful intent ("extract these", "looks like these") is B.

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

## Files

- `SKILL.md` — this file. Orchestration logic: when to fetch, where to save, how to handle paths, when to lint.
- `references/template.md` — canonical Google DESIGN.md structure: YAML front matter schema + 8-section body + token reference syntax. Owns format. Read by Path A (for the transformation target shape), Path B (extraction), and Path C (invention).
- `references/example-claude.md` — warm / editorial / quiet North Star example, in valid Google DESIGN.md format. The voice + depth + token-granularity reference. Read alongside `template.md` whenever writing a new DESIGN.md.

**Hierarchy of authority for DESIGN.md content:** `template.md` owns *structure* (token schema, section names, ordering, required fields). `example-claude.md` owns *voice + token granularity* (tone, depth, specificity, how many colors/typography levels/components a real design warrants). SKILL.md owns *workflow* (this file). When SKILL.md and template.md appear to disagree on structural details, template.md wins. When both disagree with the upstream `google-labs-code/design.md` spec, the spec wins — update template.md against it.

**Example use:** All three paths read `template.md` + `example-claude.md` together. The example sets the bar for how deep and specific each section should be, and how to use both conventional semantic token names (`primary`, `secondary`) and brand-specific descriptive tokens (`terracotta`, `parchment`) in the same file. Don't reproduce the example's aesthetic — reproduce its *care*. Brand archetypes that diverge sharply from claude's warm/editorial register (loud/luxury, brutalist, cinematic-minimal) still benefit from the example's structural discipline even if the voice differs.

## Resolution flow

When the user requests a design, run this flow in order:

### Step 1 — Check personal library

Look for `~/.claude/design-md-library/<slug>/DESIGN.md`. If it exists and the user did NOT use refresh language ("refresh X", "re-extract X", "fetch new X", "force new X", "update X", "re-fetch X"), use it directly.

**Slug rules:**
- For catalog brands, the slug = the manifest's `brand` field, exactly as written (`apple`, `linear.app`, `x.ai`, `mistral.ai`, `theverge`).
- For Path B brands not in the catalog, follow the same convention: lowercase the wordmark, collapse spaces, no hyphens (`northsignal`, `paperatlas`).
- For multi-URL Path B extracts spanning different domains, ask the user what to call it ("You're combining `linear.app` and `stripe.com` — what should the library entry be called?").
- When ambiguous, ask once.

**On a library hit:**
- Tell the user briefly: "Using your saved version of `<slug>`. Say 'refresh `<slug>`' to re-fetch."
- Report the library directory directly (`~/.claude/design-md-library/<slug>/`) and list the files in it (typically `DESIGN.md`, `preview.html`, `preview-dark.html`, and `source.txt`).
- Skip the remaining steps. Done.

**On a refresh:**
- Read `~/.claude/design-md-library/<slug>/source.txt` to determine routing:
  - `path: catalog` → Step 3a, re-fetch using the recorded URLs.
  - `path: extract` → Step 3b, re-scrape every URL in `source_urls`.
  - `path: inspiration` → Step 3c, re-run with the saved `direction` + `ref_urls`. Warn first that output may differ from the previous version (see Path C refresh caveat in Step 5).
- If `source.txt` is missing (legacy library entry from before this rule), fall back to Step 2 routing and warn the user: "No source record for `<slug>` — routing by request phrasing. Save will create a `source.txt` for next time."
- At the end of the run, ask: "Overwrite your saved version of `<slug>` with this fresh one?"

### Step 2 — Determine source path

Parse the user's request to decide between catalog (Path A), fresh extract (Path B), or inspiration (Path C):

| User said | Path |
|---|---|
| Brand name alone ("Shopify", "Apple", "Linear") | A — catalog |
| Brand-y phrasing ("design like Apple", "Apple style") | A — catalog |
| One URL ("apple.com", "northsignal.dev") | B — extract |
| Multiple URLs ("northsignal.dev and northsignal.dev/pricing") | B — extract, multi-URL |
| Explicit URL phrasing ("from apple.com", "extract apple.com") | B — extract |
| Written direction, no URLs ("moody brutalist late-90s zine") | C — inspiration |
| Written direction + URLs as refs ("inspired by northsignal, warmer") | C — inspiration |
| Blend language ("X meets Y", "X but more Z") | C — inspiration |
| "inspired by X" / "feels like X" | C — inspiration |
| Brand name + URL together ("Apple at apple.com") | Ask which they want |
| Ambiguous | Ask once |

**Decision rule when overlap is possible:** if any written direction is present, or any blend/inspiration language appears, route to C. Pure URLs with faithful intent route to B. When a single message contains both faithful and inspirational signals, ask once.

### Step 3a — Catalog path (getdesign.md source → Google DESIGN.md output)

The getdesign.md catalog (`getdesign` npm package, raw templates on unpkg) is the authoritative source for known brands. Catalog files are in **VoltAgent's 9-section format** (MIT, © VoltAgent). Path A fetches the source, then **transforms** it into Google's 8-section + YAML format so the output integrates with the rest of the pipeline (lint, token references, anchor-files DESIGN_SYSTEM.md derivation downstream).

**Do NOT scrape `getdesign.md/<slug>/design-md` for content.** That page is React-rendered and forces reconstruction. Always fetch the raw `.md` from unpkg. (Static preview pages at `getdesign.md/design-md/<brand>/preview[-dark]` are fine — they're not React-hydrated.)

**Step 1: Fetch the manifest.** The manifest is the source of truth for both routing and slugs.

```
curl -fsSL https://unpkg.com/getdesign@latest/templates/manifest.json
```

Parse the JSON. Match the user's brand to the closest entry by the `brand` field. The matching entry's `brand` is the slug; its `file` is the unpkg filename.

If there's no match, tell the user: "<Brand> isn't in the getdesign.md catalog. Want me to extract fresh from <brand>.com instead?" If yes, switch to Path B. If no, stop.

If curl fails (network, unpkg outage, proxy), fall back to `firecrawl_scrape(url=..., formats=["rawHtml"])` and parse the `rawHtml` field as JSON. Verify the fallback output is raw JSON, not wrapped or HTML-escaped — if Firecrawl wraps the content, strip the wrapper before parsing. If Firecrawl tools aren't loaded, call `tool_search` with query `"firecrawl scrape"`.

**Step 2: Fetch the source DESIGN.md and previews.**

```
mkdir -p /tmp/design/<brand>/source
curl -fsSL https://unpkg.com/getdesign@latest/templates/<file>  -o /tmp/design/<brand>/source/voltagent.md
curl -fsSL https://getdesign.md/design-md/<brand>/preview       -o /tmp/design/<brand>/preview.html      || true
curl -fsSL https://getdesign.md/design-md/<brand>/preview-dark  -o /tmp/design/<brand>/preview-dark.html || true
```

The VoltAgent-format source is kept verbatim at `source/voltagent.md` for reference and audit. Previews are catalog-authoritative — fetched as-is from getdesign.md (they reflect the brand's actual visual identity; never regenerate them for Path A).

**Step 3: Transform VoltAgent source → Google format DESIGN.md.**

Read `references/template.md` and `references/example-claude.md` first so the transformation target is fresh in mind. Then process the VoltAgent source section-by-section:

| VoltAgent (source) | Google (output) | Transformation |
|---|---|---|
| § 1 Visual Theme & Atmosphere | `## Overview` | Direct: prose paragraphs + Key Characteristics list, structurally unchanged |
| § 2 Color Palette & Roles | YAML `colors:` + `## Colors` prose | Extract every hex value into the YAML map (preserve evocative names as token keys; add Google-conventional aliases `primary`/`secondary`/`tertiary`/`neutral`/`surface`/`on-surface`/`error` where the role is clear). Restate the palette prose in `## Colors` with `{token.references}` inline |
| § 3 Typography Rules | YAML `typography:` + `## Typography` prose | Each row of the source's typography table becomes a typography token in YAML (`fontFamily`, `fontSize`, `fontWeight`, `lineHeight`, `letterSpacing` per the spec). Restate principles in `## Typography` prose |
| § 4 Component Stylings | YAML `components:` + `## Components` prose | Each named button/card/input variant becomes a `components.<name>` entry. Use `{token.references}` for token-derived values. Use `-hover`, `-active` sibling token names for interaction-state variants |
| § 5 Layout Principles (spacing scale) | YAML `spacing:` + `## Layout` prose | Extract the spacing scale into YAML; restate layout philosophy in `## Layout` prose |
| § 5 Layout Principles (border radius scale) | YAML `rounded:` + `## Shapes` prose | Extract radius scale into YAML; describe the named-scale system in `## Shapes` |
| § 6 Depth & Elevation | `## Elevation & Depth` | Direct — typically no YAML tokens, just prose |
| § 7 Do's and Don'ts | `## Do's and Don'ts` | Direct — bullet list, preserve brand-specific guidance verbatim |

Drop these from output (VoltAgent-specific, no Google equivalent):
- Section 4's "Iconography" subsection if present — fold into Components prose if site-distinctive, otherwise drop
- Section 9 "Iteration Guide" — superseded by `npx @google/design.md@latest lint`/`diff` tooling

Save the transformed result to `/tmp/design/<brand>/DESIGN.md`.

Continue to Step 4 (lint runs there before the report).

### Step 3b — Extract path (Firecrawl + Google DESIGN.md template)

Path B accepts one or more URLs. For each URL, run `firecrawl_scrape` with the parameters below. These parameters are tuned for design extraction — don't strip them.

```
firecrawl_scrape(
  url=<target-url>,
  formats=["html", "markdown", "screenshot@fullPage"],
  onlyMainContent=false,   # need nav, footer, hero — full chrome
  waitFor=2000             # let webfonts and CSS settle
)
```

Why these parameters: `onlyMainContent=false` keeps nav and footer where brand identity lives most clearly; the `screenshot@fullPage` is needed to cross-reference CSS values against visual reality; `waitFor` lets webfonts settle so typography reads correctly.

For JS-heavy sites add `actions: [{type: "wait", milliseconds: 3000}]`. For sites with cookie banners, dismiss them via a click action so the screenshot isn't dominated by the modal.

Use `firecrawl_scrape` specifically — not `firecrawl_extract` (it returns LLM-interpreted data, not raw page) and not `firecrawl_crawl` (overkill for a single page).

**Then study the site like a designer, not a parser.** Before extracting tokens, articulate what makes this site recognizable:

- Atmosphere — warm/cool, editorial/technical, quiet/loud, premium/playful
- Typographic personality — serif/grotesk, tracking, custom typeface
- Color philosophy — achromatic + accent? warm-shifted? saturated gradients? single brand color saturating everything?
- Surface treatment — pure white, off-white, parchment, true black, soft black, dual?
- Decorative language — illustrations, photography, geometric shapes, code samples?
- Distinctive components — signature patterns unique to this site

Cross-reference parsed CSS values against the screenshot. If a color appears dominant visually but rare in CSS, it's probably a gradient or image — note it anyway.

**Write the DESIGN.md following `references/template.md`.** template.md owns the Google format (YAML schema + 8 sections + token reference syntax) and quantity guidance. Don't reproduce its rules from memory — read it. Read `references/example-claude.md` alongside it as the North Star for voice, depth, and token granularity. The output must:

1. Start with bare YAML front matter between `---` fences (no code-fence wrapping).
2. Define `colors`, `typography`, and at least the `colors.primary` token. Other token groups (`rounded`, `spacing`, `components`) appear if the brand warrants them.
3. Use both **Google-conventional semantic names** (`primary`, `secondary`, `tertiary`, `neutral`, `surface`, `on-surface`, `error`) AND **brand-specific descriptive names** (`terracotta`, `parchment`, etc.) as token keys — the same value can have multiple keys. See `example-claude.md` for the pattern.
4. Use `{path.to.token}` references in prose and in component property values where the brand has consistent role-to-token mapping.
5. Emit all 8 sections in order: Overview, Colors, Typography, Layout, Elevation & Depth, Shapes, Components, Do's and Don'ts.

**Multi-URL synthesis rules.** When more than one URL was scraped, follow these rules instead of averaging:

1. **Atmosphere, color philosophy, typography** → derive from the *primary* URL (first one given, or the explicitly-flagged "main" one). These are brand-level, not page-level.
2. **Components** → pull each component from whichever page features it most prominently. If the user said "pricing table from /pricing", that page wins for the pricing table component. If they didn't annotate, take the best example you find across pages.
3. **When surfaces genuinely conflict** (e.g., marketing uses serif headlines, docs uses grotesk) → note it inline: "Headlines use serif on marketing surfaces, grotesk on docs. For app-like UI, default to grotesk."
4. **Don't average. Don't blend. Don't invent a synthesis that doesn't exist on any page.**

Save to `/tmp/design/<slug>/DESIGN.md`.

**Generate previews.** After writing DESIGN.md, generate `preview.html` (and `preview-dark.html` if the source uses dark mode) as a self-contained HTML demo of the design. Include: typography hierarchy (h1 → body → caption), the full color palette as labeled swatches, key components per the DESIGN.md Components section, and a representative page-level composition that uses the tokens together. Inline all styles — preview files must render standalone, no external CSS or assets. Save them alongside the DESIGN.md in the staging directory. Path B always ships with previews — they're how the user sanity-checks the extraction before saving.

Continue to Step 4.

### Step 3c — Inspiration path (refs as mood, not source)

Path C accepts written direction with optional URL refs. The direction is the *brief*; the refs are *mood boards*. Output is an original DESIGN.md that draws on the refs without reproducing them. This is the only path where invention is the goal.

**Step 1 — parse the direction.** From the user's words, extract:

- Atmosphere (warm/cool, quiet/loud, editorial/technical, premium/playful)
- Era / archetype if implied (late-90s zine, brutalist, swiss-grid, art-deco, vaporwave)
- Required attributes ("must feel calm", "high contrast", "no gradients")
- Hard constraints ("dark mode only", "serif only", "monochrome")

If the direction is too thin to act on (e.g., bare "make something cool"), ask one focused question before scraping or writing. Don't fabricate intent.

**Step 2 — scrape ref URLs (if any).** Same params as Path B:

```
firecrawl_scrape(
  url=<ref-url>,
  formats=["html", "markdown", "screenshot@fullPage"],
  onlyMainContent=false,
  waitFor=2000
)
```

But treat outputs as *mood references*, not extraction sources. You're noting atmosphere, color philosophy, and one or two distinctive moves per ref — not extracting tokens to reproduce. If you find yourself copying a hex value from a ref unchanged, you've slipped into Path B territory; either shift the value to match the direction or call out that you're echoing a ref deliberately.

**Step 3 — read references.** Read `references/template.md` (Google format + token schema) and `references/example-claude.md` (voice + depth + token-granularity reference).

**Step 4 — write the DESIGN.md.** Follow Google DESIGN.md format per `references/template.md` (YAML front matter + 8 sections + token references). Two extra requirements unique to Path C:

1. **Add an "Influences" subsection at the top of Section 1 (Atmosphere).** Format:

```
## Influences

**Direction (from user):** <verbatim or near-verbatim — the user's own words, lightly cleaned up>

**References:**
- `northsignal.dev` — informed brutalist surface treatment and monospace typography
- `<ref 2 url or phrase>` — informed warm accent palette
- "late-90s zine culture" — informed high-contrast slab headlines
```

2. **Cite influences inline when a token is clearly drawn from a ref.** Example: `terracotta accent (#C04A2B) — drawn from northsignal.dev's primary action color, shifted warmer to match the direction's "earthy" attribute`. Don't cite every token — only where the link is real and specific.

**Invent freely otherwise.** Tokens that aren't ref-derived should be original, grounded in the direction's atmosphere/archetype. Don't invent random values; ground them. "Earthy" + "calm" should not produce neon.

**Don't claim a ref's exact tokens.** If you scraped northsignal.dev and saw `#0A0A0A`, don't put `#0A0A0A` in the output and call it inspired. Either reproduce it (and acknowledge this is closer to Path B) or shift it meaningfully.

**Slug.** Path C slugs collide easily with real brand names if not namespaced. Ask the user: "What should I call this design? (e.g., `myproject-v1`, `client-acme`, `moody-zine`.)" Avoid auto-deriving a slug from a ref — `northsignal` would shadow a future Path B extract of the real site. The library is the user's, but uniqueness is on us.

Save to `/tmp/design/<slug>/DESIGN.md`.

**Generate previews.** Same as Path B — `preview.html` always, `preview-dark.html` if dark mode fits the direction.

Continue to Step 4.

### Step 4 — Lint, then deliver and report

**Step 4a — Lint gate (mandatory, all paths).** Before producing the report or offering the save prompt, run lint against the staged DESIGN.md:

```bash
npx @google/design.md@latest lint /tmp/design/<slug>/DESIGN.md
```

The CLI outputs JSON with `findings` and a `summary`. Three cases:

| `summary.errors` | What it means | Action |
|---|---|---|
| `> 0` | Hard fail — the DESIGN.md is structurally invalid | **Stop. Do not advance to Step 4b.** Surface the errors to the user verbatim, explain that promotion is blocked, and offer to fix-and-re-emit. For Path A, an error usually means the source-to-Google transformation lost or malformed a token; re-do that section. |
| `0`, `warnings > 0` | Soft fail — surface for triage | Include warnings in the Step 4b report so the user can decide whether they're acceptable. Common warnings: WCAG contrast below AA, missing recommended sections. Promotion proceeds unless the user objects. |
| `0`, `warnings == 0` (info only) | Pass | Proceed silently to Step 4b. |

If `npx @google/design.md@latest` fails to install (network, registry, Node version mismatch), surface the error and fall back to "structural sanity check only" — re-read the DESIGN.md, verify the YAML parses, verify all 8 sections are present (or explicitly omitted), verify token references resolve. Do not silently skip the gate.

**Step 4b — Report.**

**For Path A (catalog hits):** keep the report minimal. The user asked for a known brand and got that brand transformed into Google format — no editorial framing needed.

- Staging directory path (`/tmp/design/<brand>/`)
- Files present: `DESIGN.md` (transformed), `source/voltagent.md` (original kept for reference), `preview.html`, `preview-dark.html` (catalog-authoritative, when present)
- Lint result summary (errors: 0, warnings: N, infos: N)
- Notable transformations from the source (e.g., "merged Section 4's Iconography subsection into Components prose", "extracted 21 colors into YAML")
- Recommend: "Open `/tmp/design/<brand>/preview.html` in your browser to see the design rendered before you decide to save."

**For Path B (fresh extracts):** report what the skill produced and where its judgment was involved.

- Staging directory path (`/tmp/design/<slug>/`)
- Files present: `DESIGN.md`, `preview.html`, `preview-dark.html` (when generated)
- Lint result summary (errors: 0, warnings: N, infos: N)
- 3–5 bullet aesthetic summary ("warm parchment canvas, terracotta accent, serif headlines at weight 500, ring-shadow depth system")
- Uncertainty flags ("couldn't isolate hover state for primary button — used inferred 10% darker variant; flagged in the doc")
- For multi-URL extracts: which URL contributed which sections, and any cross-surface conflicts noted inline

**For Path C (inspiration runs):** report the invented design and how the refs shaped it.

- Staging directory path (`/tmp/design/<slug>/`)
- Files present: `DESIGN.md`, `preview.html`, `preview-dark.html` (when generated)
- Lint result summary (errors: 0, warnings: N, infos: N)
- 3–5 bullet aesthetic summary, same shape as Path B
- **Influence map:** one line per ref, what it contributed (matches the Influences section in the doc)
- **Invention flags:** call out the original choices ("the brass-on-charcoal palette is original — neither ref used it; chosen to match 'warm, prestigious' in the direction")
- Uncertainty flags where direction was thin and you made a judgment call

**Downstream handoff note.** After the design is saved (Step 5), the next step before `anchor-files` is running `mockups` — it generates two-pass anchor-strategy briefs from `DESIGN.md` + `UX.md` + FEATURES and writes them to `.arsenal/strategy/mockup-briefs/`. The user feeds each brief into Claude Design / Stitch / Open Design / v0, saves outputs to `.arsenal/design/mockups/`. `arsenal-build:design` reads that directory; the visual fidelity gates (especially the iOS simulator-mediated one) are substantially stronger when concrete mockups are present. Mention this in the final report so the user knows to run `mockups` next.

### Step 5 — Save prompt

Each path uses a slightly different prompt:

- **Catalog (Path A):** *"Save to your library as `<brand>`? (Press enter to accept, or provide a different slug like `<brand>-2026`.)"* — the manifest slug is canonical, so renaming is uncommon.
- **Extract (Path B):** *"Save to your library as `<slug>`? (Press enter to accept, or provide a different slug like `client-x` or `<slug>-v2`.)"* — renaming is more common here for client work or version-tagged variants.
- **Inspiration (Path C):** *"Save to your library as `<slug>`? (Press enter to accept, or provide a different slug.)"* — slug was already chosen during Step 3c, so this is mostly a confirm.

**Write `source.txt` first, then `mv`.** Write `source.txt` into the *staging* directory (`/tmp/design/<slug>/source.txt`) before promoting — either at the end of Step 3a/3b or as the first action of Step 5. A single `mv` then promotes `DESIGN.md`, both preview HTMLs, and `source.txt` as one atomic unit. Never write `source.txt` into the library directory after the `mv` — if that write fails, the library entry ends up with no origin record and refresh breaks silently.

**On yes:** move the entire staging directory: `mkdir -p ~/.claude/design-md-library && mv /tmp/design/<slug> ~/.claude/design-md-library/<slug>`. Use `mv`, not `cp` — that's what keeps the library the single long-lived copy.

**On yes with rename:** `mv /tmp/design/<slug> ~/.claude/design-md-library/<new-slug>`. The destination basename in the `mv` command becomes the library slug — rename happens atomically as part of the promotion, no separate step needed.

**On no:** leave `/tmp/design/<slug>/` in place. The OS will clean `/tmp` on its own schedule (macOS: ~3 days). If the user later wants to save what they declined, they'll need to re-run the skill — the staging dir is not guaranteed to still exist.

`source.txt` records origin and lets refresh re-fetch from the same source without consulting Step 2's routing.

For Path A:

```
path: catalog
brand: apple
manifest: https://unpkg.com/getdesign@latest/templates/manifest.json
design_md: https://unpkg.com/getdesign@latest/templates/apple.md
preview: https://getdesign.md/design-md/apple/preview
preview_dark: https://getdesign.md/design-md/apple/preview-dark
saved_at: 2026-04-15
```

For Path B (always use `source_urls` as a list, even with a single URL):

```
path: extract
brand: northsignal
source_urls:
  - https://northsignal.dev
  - https://northsignal.dev/pricing
saved_at: 2026-04-15
```

For Path C (direction is the source of truth; refs are mood; both are needed for refresh):

```
path: inspiration
brand: myproject-v1
direction: |
  moody brutalist, late-90s zine culture, but warmer.
  high contrast, slab serif headlines, no gradients.
  must feel calm and prestigious, not loud.
ref_urls:
  - https://northsignal.dev
  - https://example.com/typography-page
ref_phrases:
  - "late-90s zine culture"
  - "warm prestigious"
saved_at: 2026-05-04
```

`source.txt` doubles as a useful "where did this come from?" lookup the user can `cat` anytime.

**Refresh saves (overwrite case):** the prompt is *"Overwrite your saved version of `<slug>` with this fresh one?"* On yes, `rm -rf ~/.claude/design-md-library/<slug>` first, then `mv` the staging dir into place. `source.txt` is rewritten with the new fetch's URLs and updated `saved_at`.

**Path C refresh caveat.** Refreshing a Path C entry re-runs the inspiration prompt against the saved `direction` + `ref_urls`. Because invention is involved, output may differ from the original — that's not a bug. Warn the user before regenerating: *"Refreshing a Path C design re-invents from the saved direction and refs — it won't be byte-identical to the previous version. Continue?"* If they want byte-identical, they don't want a refresh, they want the existing library entry.

## Previews

The preview HTMLs visualize a design file in the browser. Not a workflow step — reference info on how previews work across the three paths.

Behavior differs by path:

- **Path A (catalog):** previews are fetched verbatim from getdesign.md in Step 3a. They are authoritative, brand-accurate, and styled by the catalog's curator. Never regenerate them.
- **Path B (fresh extract):** generated inline in Step 3b as self-contained HTML — typography hierarchy + color palette + key components + a page-level composition, all styles inlined. Always generated alongside the DESIGN.md — Path B never ships without a preview.
- **Path C (inspiration):** generated inline in Step 3c, same shape as Path B. The preview reflects the *invented* design, not any of the refs. Always generated.

When the user saves, the Step 5 `mv` sweeps preview files along with the DESIGN.md automatically — no extra handling needed.

## File layout

```
~/.claude/design-md-library/        # personal library — built up over time
├── apple/
│   ├── DESIGN.md
│   ├── preview.html
│   ├── preview-dark.html
│   └── source.txt
├── shopify/
│   ├── DESIGN.md
│   ├── preview.html
│   ├── preview-dark.html
│   └── source.txt
├── northsignal/
│   ├── DESIGN.md
│   ├── preview.html
│   └── source.txt
└── myproject-v1/                    # Path C inspiration entry
    ├── DESIGN.md
    ├── preview.html
    ├── preview-dark.html
    └── source.txt

/tmp/design/                     # ephemeral review staging (current run only)
└── <slug>/
    ├── DESIGN.md
    ├── preview.html
    ├── preview-dark.html
    └── source.txt
```

## Important behaviors

**Lint gate is mandatory.** Step 4a's `npx @google/design.md@latest lint` runs against every staged DESIGN.md before Step 4b's report. Errors block promotion to Step 5. Warnings are surfaced for triage but don't auto-block. The CLI being unavailable (network / Node version mismatch) does not bypass the gate — fall back to structural sanity check (YAML parses, all 8 sections present or explicitly omitted, token references resolve).

**Library hits are byte-identical.** Step 1 library hits return the existing DESIGN.md verbatim — no re-emission, no re-lint, no re-transformation. The library is the user's curated archive; it's already been linted at write time.

**Path A is transform-faithful, not copy-verbatim.** Catalog sources are in VoltAgent's 9-section format; output is Google's 8-section + YAML. The transformation must preserve every token, every named distinction, every brand-specific guideline in the source — only the *shape* changes. Don't paraphrase, don't "improve" the brand voice, don't editorialize the catalog curator's choices. If the source says "Near Black", your YAML has `near-black: "#141413"` and your prose still calls it Near Black. The whole value of the catalog is that someone wrote it carefully.

**Paths B and C are original writing.** B is faithful to URLs (refs ARE the source); C is invented from direction (refs INFORM the source). Both produce new DESIGN.md content in Google format — they don't have a verbatim contract.

**Library is the only long-lived location for DESIGN.md files.** Staging lives in `/tmp/design/` and is either promoted to the library via `mv` or left for OS cleanup. Never `cp` — `mv` is what keeps the library the single source of truth.

**Slug discipline matters.** Consistent slugs are what make library hits work. Catalog slugs come straight from the manifest's `brand` field — no translation, no inference. Path B slugs follow the same convention (lowercase, collapse spaces, no hyphens) so library entries from both paths look uniform. Path C slugs are user-chosen and should avoid colliding with real brand names — `myproject-v1`, `client-acme`, `moody-zine` good; bare `northsignal` for an inspiration design bad (it would shadow a future Path B extract of the real site).

**Don't overstep on the slug.** If a user says "save as my-cool-brand-v2", honor it exactly. The library is theirs to organize.

**Cap invented DESIGN.md (Path C) at ~500 lines.** Long brand specs become unscannable and bloat downstream context — the next skill (anchor-files / DESIGN_SYSTEM.md) will balloon trying to implement them. Sweet spot: 100–400 lines for Path C. Paths A and B are verbatim — length is whatever the source is, never truncate to fit. If a Path C invention genuinely needs more than 500 lines, split into `DESIGN.md` (core tokens + voice) + sibling files (e.g., `MOTION.md`, `COMPONENTS.md`) and cross-link.

## Anti-patterns

- **Don't editorialize the brand on Path A.** The transformation reshapes 9→8 sections + YAML, but the brand's identity — colors, typography decisions, named distinctions, anti-patterns — must come through unchanged. Rewriting the catalog curator's voice with your own is the single worst failure mode of Path A; it produces a plausible-looking document that doesn't match the brand's actual identity.
- **Don't skip the lint gate.** Every emit (A, B, C) runs through `npx @google/design.md@latest lint` before Step 4b's report. Errors block promotion; soft-failing past an error is a defect.
- **Don't blur B and C.** Path B reproduces a site faithfully; Path C invents using refs as mood. If a user provides URLs and written direction, route to C — copying ref tokens unchanged into a "C" output is sloppy invention. If a user provides only URLs and faithful intent, route to B — inventing original tokens when they wanted faithful extraction is the opposite failure. When in doubt, ask.
- **Don't skip the Influences section on Path C.** It's how the user audits whether the invention is grounded. Without it, the output looks like Path B writing pretending to be original.
- **Don't auto-derive a Path C slug from a ref URL.** `northsignal` for a design inspired by northsignal.dev shadows a future Path B extract of the real site. Always ask the user for a namespace-safe slug.
- **Don't editorialize on Path A.** The skill's job for catalog hits is delivery, not commentary. If the source has duplicate entries or unusual structure, that's the catalog's choice — leave it and don't flag it.
- **Don't reproduce template.md's rules in SKILL.md.** When working on Path B, read template.md fresh. Inlining its rules in SKILL.md creates drift; the two have already drifted apart once and shouldn't again.
- **Don't invent values.** If you can't find a hex code or a hover state, mark it `[inferred]` with reasoning. Confident guesses kill the doc's credibility.
- **Don't paraphrase CSS into vague design-speak.** "Soft shadows" tells an agent nothing. `0 1px 2px rgba(0,0,0,0.05), 0 4px 8px rgba(0,0,0,0.08)` plus "extremely soft, suggesting floating rather than casting" is the spec.
- **Don't auto-save without asking.** Always prompt. The library is a curated personal resource, not an automatic dumping ground.
- **Don't average across surfaces in multi-URL extracts.** If marketing and docs disagree on a token, note both inline. Synthesizing a value that exists on no page is worse than reporting the conflict.

## Notes on Firecrawl quirks

- **Don't scrape `getdesign.md/<brand>/design-md` for markdown.** The page is React-rendered and forces reconstruction. Use unpkg (Step 3a) instead. The static preview pages at `getdesign.md/design-md/<brand>/preview[-dark]` are fine — they're not React-hydrated.
- Some sites block scraping; if you get a 403 or empty content, try `formats=["screenshot@fullPage"]` alone and infer more from the visual.
- Cookie banners can dominate screenshots. Add a click action to dismiss them — Step 3b's `actions` parameter is where this goes.
- When choosing the primary URL for a single-URL extract, the marketing page usually carries the strongest brand identity. For multi-URL extracts, list each surface explicitly (marketing, pricing, docs) and the synthesis rules in Step 3b will handle the composition.
- Heavy JS apps may need `waitFor` of 5000ms+ to settle webfonts and animations.
