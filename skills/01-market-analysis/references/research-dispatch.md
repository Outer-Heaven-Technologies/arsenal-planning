# Research Investigation — Worker Framing

This file is the per-investigation framing used during Step 2c (deep market/customer research) and Step 3a (per-competitor deep analysis).

The market-analysis orchestrator gives this framing to each fresh-context worker. When workers are unavailable, the initiating context follows it inline for one bounded investigation at a time. It defines citation requirements, source-quality grading, tool preferences, research strategy, and the return format.

**Use:** provide this framing first, followed by the investigation's specific question and source priorities.

---

## Research strategy (for every investigation)

**Start broad, then narrow.** Open with short, broad queries to map the landscape. Once the shape is clear, narrow to specific questions. Don't go deep on a thread until you've surveyed the whole territory.

**Parallelize within the investigation.** Multiple tool calls in parallel where they're independent (e.g., fetch three competitor pricing pages simultaneously). Sequential only when one query informs the next.

**Stop when the marginal return drops.** If three additional searches return the same kind of result, you've saturated. Move on. Don't pad.

**Scaling cap.** 3–10 tool calls per investigation as the working range; **15 is a hard cap**. If you can't answer the question in 15 calls, the question is too broad — return what you have with confidence ratings, flag the gap, and stop.

---

## Tool preferences

**Prefer Jina MCP** when available — it returns clean structured markdown and handles JS-rendered pages better than vanilla fetch:

- `jina_reader` (or equivalent) for URL → clean markdown
- `jina_search` for query → structured search results with snippets
- Use Jina first; fall back to `WebSearch` / `WebFetch` / `Firecrawl` only when Jina is unavailable or insufficient

**Fallback order:**
1. Jina MCP (preferred)
2. Firecrawl (good for JS-heavy sites, cookie banners)
3. WebSearch + WebFetch (vanilla)
4. Direct curl (only for known-static endpoints)

Mention the tool used in citations only when it matters for source attribution (e.g., a Reddit thread accessed via Jina Reader is still attributed to Reddit, not Jina).

---

## Source-quality tier grading

**Every claim is graded on source tier.** Tier the source, not the claim. A weak claim from a Tier-1 source is more credible than a strong claim from a Tier-4 source.

| Tier | Source type | Examples | When to use |
|---|---|---|---|
| **T1** | Primary research, regulatory data, founder interviews | SEC filings, government statistics, direct customer interviews, regulatory filings, peer-reviewed research, primary survey data | Always preferred. Use whenever available. |
| **T2** | Authoritative secondary | Gartner / Forrester / IDC analyst reports, peer-reviewed academic research, audited financial reports, established trade body data | Strong support; treat as near-primary if the analyst firm has methodology disclosure |
| **T3** | Reputable industry publications | TechCrunch, The Information, Bloomberg, Wired, established trade press, company-disclosed metrics in earnings calls, HN front-page discussion | Acceptable for market-color claims; demand corroboration for quantitative claims |
| **T4** | Blogs, vendor marketing, SEO content | Company blogs, marketing pages, content-marketing-as-research, listicles, AI-generated content farms | **Flag explicitly.** Use only when no higher-tier source exists, and only for context, never for foundational claims. Note any T4 sources in §6 Risks of the dossier as "claim resting on weak source." |

**Never use:** uncited blog posts, AI-generated SEO content with no author, "studies" published as marketing collateral with no methodology, forum opinions without supporting data.

**The investigator's job is to find T1/T2 sources first.** If after a reasonable search effort nothing higher than T4 exists for a critical claim, that itself is a finding — return "no authoritative source found" with confidence L, not a fabricated number.

---

## Citation requirements

**Every claim has a citation.** Format:

```
[source: <URL>, tier: T1|T2|T3|T4, confidence: H|M|L]
```

Inline at the end of the sentence containing the claim. Examples:

```markdown
The global note-taking app market is estimated at $1.2B with 18% YoY growth
through 2027 [source: https://www.grandviewresearch.com/industry-analysis/note-taking-apps,
tier: T3, confidence: M].

Anthropic's API pricing tripled for Claude Opus between 2024 and 2025
[source: https://www.anthropic.com/pricing-history-archive, tier: T1, confidence: H].
```

**Confidence ratings:**
- **H (High):** primary source with methodology disclosure; or independent corroboration from 2+ T1/T2 sources
- **M (Medium):** single T2/T3 source; or T1 source with limited methodology disclosure; or estimate derived from solid primary data
- **L (Low):** T3 single source; T4 source even if multiple; inferred estimate without primary data backing

**Uncited claims are rejected.** Either find a source or remove the claim. If a claim is "common knowledge" (water is wet, the iOS App Store takes 30%), still cite — readers may not share the same baseline knowledge.

**Don't fabricate citations.** If you can't find a source, say so. A claim with `[source: not found, confidence: L]` is more credible than a fabricated URL.

---

## Investigation return format

Each worker returns one self-contained report directly to the market-analysis orchestrator. Inline fallback work produces the same report shape in working context.

**Required sections in the report:**

1. **Question** — restate the specific research question this investigation answered
2. **Method** — 2-3 sentence summary of how the research was conducted (queries, sources consulted, what was excluded)
3. **Findings** — the actual content, organized by sub-question if needed. **Every claim cited inline** per the citation format above.
4. **Confidence summary** — bulleted list of the 3-5 most decision-critical claims, each with confidence rating and source tier
5. **Gaps** — what couldn't be answered, what would require primary research or more time
6. **Recommendations** — 2-4 bullets on what this finding implies for the build (this is what gets pulled into the dossier's "So what?" closures)
7. **Files touched** — every source URL accessed, tier-tagged

Length: 1500–3000 words target. Longer means the investigation was too broad; trim and rerun a narrower follow-up if needed.

---

## When the investigation is too broad

If the question can't be answered in 15 tool calls, **return early with what you have** and flag the scope problem:

```markdown
## Status: SCOPE_TOO_BROAD

The question "What's the global market size for productivity apps?" is too broad
to answer in this investigation. The market spans 12+ distinct categories (todo, notes,
calendars, etc.) each with separate sizing.

Recommended next step: split into 2-3 narrower investigations by category, or
narrow to the specific segment relevant to this product.

## What I found in 8 calls
[Partial findings with citations]
```

The orchestrator either reruns narrower investigations or accepts the partial result and adjusts the dossier accordingly.

---

## Anti-patterns

- **Don't fabricate numbers.** If TAM is unknown, say so with confidence L. A wrong number with confidence H is worse than no number.
- **Don't pad with content-farm sources.** A 2000-word investigation built on Medium posts is less credible than a 500-word investigation built on 3 SEC filings.
- **Don't go deep on one source.** Spread across sources. If you've read 6 pages of one site, you've spent too long there — move on.
- **Don't synthesize across investigations.** Each investigation answers its own question independently. Cross-investigation synthesis happens in mvp's Step 4, not inside an investigation.
- **Don't infer beyond the data.** Stick to what sources say. If asked about pricing strategy and sources only cover current pricing, return current pricing with a note about strategy-inference being out of scope.
- **Don't use vague citations.** `[source: industry research]` is rejected. URL + tier + confidence, or it doesn't count.
