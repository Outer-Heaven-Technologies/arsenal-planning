---
name: design
description: Produces a `DESIGN.md` brand spec via three paths — fetch from the getdesign.md catalog (verbatim), extract faithfully from URLs, or invent from written direction with optional refs. Maintains a personal library at `~/.claude/design-md-library/`. Use when the user wants design tokens, a brand spec, or a DESIGN.md for a known brand or original aesthetic.
---

# Plan Design

Produces a `DESIGN.md` file. Three source paths, one personal library.

**Core principle — verbatim for existing sources, faithful for extracts, original for inspiration.** If the DESIGN.md already exists (personal library OR getdesign.md catalog), copy it byte-for-byte. Never paraphrase, restructure, re-title, or reformat. Path B produces faithful new writing extracted from real URLs. Path C produces original new writing influenced by refs but not reproducing them.

**The B-vs-C line.** Path B reproduces existing designs faithfully (single or multi-URL — refs ARE the source). Path C creates new designs using refs (URLs, words, archetypes) as inspiration — refs INFORM the source, they don't become it. If the user provides any written direction, or uses blend language ("X meets Y", "inspired by X but Z"), it's C. Pure URLs with faithful intent ("extract these", "looks like these") is B.

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

## Files

- `SKILL.md` — this file. Orchestration logic: when to fetch, where to save, how to handle paths.
- `references/template.md` — canonical 9-section DESIGN.md structure with field-level guidance. Owns format. Read by Path B before extraction and Path C before invention.
- `references/example-claude.md` — warm / editorial / quiet. Default North Star. Serif, parchment, earthy accent. Read for premium content, publishing, AI/research products.
- `references/example-apple.md` — cinematic / minimal / premium consumer. Monochrome sections, a single chromatic accent, photography-as-hero. Read for consumer hardware and luxury product brands.
- `references/example-lamborghini.md` — loud / luxury / dramatic / maximalist. Read for automotive, luxury, bold editorial, or any high-intensity brand.
- `references/preview-template.md` — HTML template for generating Path B and Path C previews.

**Hierarchy of authority for DESIGN.md content:** template.md owns *structure* (section names, ordering, format, required subsections). The three example files collectively own *voice* (tone, depth, specificity) — each covers a different aesthetic territory. SKILL.md owns *workflow* (this file). When SKILL.md and template.md appear to disagree on structural details, template.md wins.

**Example selection:** Path B reads `template.md` + **one closest-match example**, not all three. template.md's opening section spells out which example maps to which brand archetype. Reading all three per run is wasteful context; reading none at all is how voice drifts toward generic.

**Path C example selection:** same one-example default. Exception: when the user's direction explicitly mixes archetypes ("warm but loud", "editorial meets brutalist"), read two — that's the only legitimate case for multiple examples in one run.

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

### Step 3a — Catalog path (getdesign.md, fetched verbatim from unpkg)

The getdesign.md catalog is published as an npm package (`getdesign`) whose raw template files live on unpkg. This is the authoritative source. Fetch it and write it **byte-for-byte** to a temporary staging path. Do not paraphrase. Do not re-title. Do not restructure. Do not add a "source footer" or your own framing. If the file has awkward wording, that is the canonical form — leave it and don't editorialize.

**Do NOT scrape `getdesign.md/<slug>/design-md` for content.** That page is React-rendered and forces reconstruction. Always fetch the raw `.md` from unpkg. (Static preview pages at `getdesign.md/design-md/<brand>/preview[-dark]` are fine — they're not React-hydrated.)

**Step 1: Fetch the manifest.** The manifest is the source of truth for both routing and slugs.

```
curl -fsSL https://unpkg.com/getdesign@latest/templates/manifest.json
```

Parse the JSON. Match the user's brand to the closest entry by the `brand` field. The matching entry's `brand` is the slug; its `file` is the unpkg filename.

If there's no match, tell the user: "<Brand> isn't in the getdesign.md catalog. Want me to extract fresh from <brand>.com instead?" If yes, switch to Path B. If no, stop.

If curl fails (network, unpkg outage, proxy), fall back to `firecrawl_scrape(url=..., formats=["rawHtml"])` and parse the `rawHtml` field as JSON. **Verify the fallback output is raw JSON, not wrapped or HTML-escaped** — if Firecrawl wraps the content (e.g., in `<pre>` tags or escapes `<` and `>`), strip the wrapper before parsing. Same rule applies to every Firecrawl fallback in this skill. If Firecrawl tools aren't loaded, call `tool_search` with query `"firecrawl scrape"`. If Firecrawl isn't installed, call `suggest_connectors`.

**Step 2: Fetch the DESIGN.md and previews.** All three files are static — curl is canonical.

```
mkdir -p /tmp/design/<brand>
curl -fsSL https://unpkg.com/getdesign@latest/templates/<file>          -o /tmp/design/<brand>/DESIGN.md
curl -fsSL https://getdesign.md/design-md/<brand>/preview               -o /tmp/design/<brand>/preview.html      || true
curl -fsSL https://getdesign.md/design-md/<brand>/preview-dark          -o /tmp/design/<brand>/preview-dark.html || true
```

The `|| true` guards on the previews ensure a missing dark variant never aborts the run. The DESIGN.md fetch must succeed.

If curl fails on any fetch, fall back to `firecrawl_scrape(url=..., formats=["rawHtml"])` and write the `rawHtml` field byte-for-byte. **Verify the fallback output is raw markdown / raw HTML, not wrapped or escaped** — if Firecrawl wraps the content (e.g., in `<pre>` tags or HTML-escapes `<` and `>`), strip the wrapper before saving. Verbatim fidelity is the whole point of Path A.

Continue to Step 4.

### Step 3b — Extract path (Firecrawl + 9-section template)

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

**Write the DESIGN.md following `references/template.md`.** template.md owns the 9-section structure, required subsections, format conventions, and quantity guidance. Don't reproduce its rules from memory — read it. Read `references/example-claude.md` alongside it as the North Star for voice and depth.

**Multi-URL synthesis rules.** When more than one URL was scraped, follow these rules instead of averaging:

1. **Atmosphere, color philosophy, typography** → derive from the *primary* URL (first one given, or the explicitly-flagged "main" one). These are brand-level, not page-level.
2. **Components** → pull each component from whichever page features it most prominently. If the user said "pricing table from /pricing", that page wins for the pricing table component. If they didn't annotate, take the best example you find across pages.
3. **When surfaces genuinely conflict** (e.g., marketing uses serif headlines, docs uses grotesk) → note it inline: "Headlines use serif on marketing surfaces, grotesk on docs. For app-like UI, default to grotesk."
4. **Don't average. Don't blend. Don't invent a synthesis that doesn't exist on any page.**

Save to `/tmp/design/<slug>/DESIGN.md`.

**Generate previews.** After writing DESIGN.md, generate `preview.html` (and `preview-dark.html` if the source uses dark mode) per `references/preview-template.md`. Save them alongside the DESIGN.md in the staging directory. Path B always ships with previews — they're how the user sanity-checks the extraction before saving.

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

**Step 3 — pick example(s).** Read `references/template.md`. Read the closest-match example file for the direction's primary voice. If the direction explicitly mixes archetypes, read two — see "Path C example selection" above.

**Step 4 — write the DESIGN.md.** Follow `references/template.md`'s 9-section structure. Two extra requirements unique to Path C:

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

### Step 4 — Deliver and report

**For Path A (catalog hits):** keep the report minimal. The user asked for a known brand and got that brand verbatim — no editorial framing needed.

- Staging directory path (`/tmp/design/<brand>/`)
- Files present: `DESIGN.md`, `preview.html`, `preview-dark.html` (when present)
- Recommend: "Open `/tmp/design/<brand>/preview.html` in your browser to see the design rendered before you decide to save."

**For Path B (fresh extracts):** report what the skill produced and where its judgment was involved.

- Staging directory path (`/tmp/design/<slug>/`)
- Files present: `DESIGN.md`, `preview.html`, `preview-dark.html` (when generated)
- 3–5 bullet aesthetic summary ("warm parchment canvas, terracotta accent, serif headlines at weight 500, ring-shadow depth system")
- Uncertainty flags ("couldn't isolate hover state for primary button — used inferred 10% darker variant; flagged in the doc")
- For multi-URL extracts: which URL contributed which sections, and any cross-surface conflicts noted inline

**For Path C (inspiration runs):** report the invented design and how the refs shaped it.

- Staging directory path (`/tmp/design/<slug>/`)
- Files present: `DESIGN.md`, `preview.html`, `preview-dark.html` (when generated)
- 3–5 bullet aesthetic summary, same shape as Path B
- **Influence map:** one line per ref, what it contributed (matches the Influences section in the doc)
- **Invention flags:** call out the original choices ("the brass-on-charcoal palette is original — neither ref used it; chosen to match 'warm, prestigious' in the direction")
- Uncertainty flags where direction was thin and you made a judgment call

**Downstream handoff note.** After the design is saved (Step 5), the next step before `anchor-files` is running `mockups` — it generates two-pass anchor-strategy briefs from `DESIGN.md` + `UX.md` + FEATURES and writes them to `planning/mockup-briefs/`. The user feeds each brief into Claude Design / Stitch / Open Design / v0, saves outputs to `docs/mockups/`. `design-{web,ios}` reads that directory; the visual fidelity gates (especially the iOS simulator-mediated one) are substantially stronger when concrete mockups are present. Mention this in the final report so the user knows to run `mockups` next.

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

- **Path A (catalog):** previews are fetched verbatim from getdesign.md in Step 3a. They are authoritative, brand-accurate, and styled by the catalog's curator. Never regenerate them from the template.
- **Path B (fresh extract):** generated in Step 3b from `references/preview-template.md`. Always generated alongside the DESIGN.md — Path B never ships without a preview.
- **Path C (inspiration):** generated in Step 3c from `references/preview-template.md`, same as Path B. The preview reflects the *invented* design, not any of the refs. Always generated.

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

**Verbatim means verbatim.** For both Path A (catalog) and library hits, the file you deliver must be byte-identical to the source. No paraphrasing, no re-titling, no "tone improvements," no Section 9 restructuring, no adding a "Source:" footer. If the source has 9 sections, yours has 9. If it has 4, yours has 4. If it says "Near Black," yours says "Near Black." The whole value of a catalog is that someone else wrote it carefully — do not overwrite that voice with your own.

**Library is the only long-lived location for DESIGN.md files.** Staging lives in `/tmp/design/` and is either promoted to the library via `mv` or left for OS cleanup. Never `cp` — `mv` is what keeps the library the single source of truth.

**Slug discipline matters.** Consistent slugs are what make library hits work. Catalog slugs come straight from the manifest's `brand` field — no translation, no inference. Path B slugs follow the same convention (lowercase, collapse spaces, no hyphens) so library entries from both paths look uniform. Path C slugs are user-chosen and should avoid colliding with real brand names — `myproject-v1`, `client-acme`, `moody-zine` good; bare `northsignal` for an inspiration design bad (it would shadow a future Path B extract of the real site).

**Don't overstep on the slug.** If a user says "save as my-cool-brand-v2", honor it exactly. The library is theirs to organize.

**Cap invented DESIGN.md (Path C) at ~500 lines.** Long brand specs become unscannable and bloat downstream context — the next skill (anchor-files / DESIGN_SYSTEM.md) will balloon trying to implement them. Sweet spot: 100–400 lines for Path C. Paths A and B are verbatim — length is whatever the source is, never truncate to fit. If a Path C invention genuinely needs more than 500 lines, split into `DESIGN.md` (core tokens + voice) + sibling files (e.g., `MOTION.md`, `COMPONENTS.md`) and cross-link.

## Anti-patterns

- **Don't paraphrase catalog or library content.** Ever. If the source file exists (Path A or library), copy it verbatim. Rewriting it is the single worst failure mode of this skill — it produces a plausible-looking document that doesn't match what the user saw on getdesign.md and destroys the authority of the catalog. Paths B and C are the only paths that generate new writing (B faithful to URLs, C invented from refs).
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
