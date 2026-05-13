# Industry Skeletons Reference

> **Source:** Industry patterns and anti-patterns adapted from [ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) (MIT). Section ordering, component lists, and conversion models are extended from the source data.

## How to use this file

This file is the source of truth for `UX.md` generation in the `ux-web` skill (marketing/website surface). Three tiers of detail:

1. **Full sections** (below) — for industries built often. Each has conversion model, sections in order with rationale, components needed, anti-patterns, and cross-references.
2. **Compressed appendix** (bottom of file) — one-line entries for ~150 other industries. Use as research seeds for tier 3 of the `UX.md` resolution chain.
3. **Not in file at all** — fall through to tier 4: research from scratch using the closest existing section as a structural template, then offer to promote to a full section here.

The skill should grep for `## <industry>` headers when looking up a match. Names follow Pro Max's taxonomy (e.g., "SaaS (General)", "Fintech/Crypto", "AI/Chatbot Platform") for consistency with the appendix table.

App-specific skeletons (productivity tools, habit trackers, mood trackers, meditation apps, etc.) live in the sibling skills `ux-app` and `ux-ios`.

## Index of full sections

- [SaaS (General)](#saas-general)
- [Micro SaaS](#micro-saas)
- [Fintech/Crypto](#fintechcrypto)
- [AI/Chatbot Platform](#aichatbot-platform)
- [Developer Tool / IDE](#developer-tool--ide)
- [Marketing Agency](#marketing-agency)

---

## SaaS (General)

**Conversion model:** Demo-driven trust, low-friction trial signup
**Primary CTA:** Start free trial (no credit card required)
**Pattern:** Hero + Features + CTA
**Style direction:** Glassmorphism + Flat Design, trust blue + accent contrast, professional typography with clear hierarchy

### Sections (in order)
1. **Hero** — Headline (problem solved in one line), subhead (specificity), primary CTA, product visual or screenshot. Above the fold.
2. **Logo strip** — 5-8 customer logos for instant credibility. Especially critical for new entrants.
3. **Feature grid** — 3-6 features. Icon + 2-line description per feature. Group by user benefit, not by technical capability.
4. **Use cases / personas** — Brief vignettes showing the product in context. Optional but high-value for B2B.
5. **Social proof** — 1-3 testimonials with photo + name + role + company. Quantitative outcomes preferred ("cut onboarding time 40%").
6. **Pricing teaser or full pricing** — Either link to dedicated pricing page or show 3 tiers inline. Always show pricing — hiding it kills conversion for self-serve.
7. **Final CTA** — Same as hero CTA, restated. Optionally with secondary "talk to sales" for enterprise path.
8. **Footer** — Standard.

### Components needed
- Hero with primary CTA and product visual slot
- Customer logo strip
- Feature card (icon + title + description)
- Testimonial card (photo + quote + attribution)
- Pricing tier card
- Sticky nav with persistent CTA

### Anti-patterns
- Excessive animation or parallax — B2B buyers want clarity, not spectacle
- Dark mode by default — light mode is the B2B default; offer dark as a toggle
- Hiding pricing behind "contact sales" for self-serve products
- Generic stock photography in the hero
- Lead with feature counts ("100+ integrations") instead of outcomes

### Cross-references
- DESIGN_SYSTEM.md → Hero, feature card, testimonial card, pricing card components
- ARCHITECTURE.md → Trial signup flow, billing integration (Stripe), email verification

---

## Micro SaaS

**Conversion model:** Founder-led trust, scroll-driven engagement, single-CTA focus
**Primary CTA:** Start free trial OR Sign up — pick ONE
**Pattern:** Hero-Centric + Trust
**Style direction:** Motion-Driven + Vibrant blocks, bold primaries with accent contrast, modern energetic typography

Micro SaaS lives or dies on personality. Differs from general SaaS in three ways: (1) the founder is often part of the brand, (2) the audience is narrower so the messaging can be sharper, (3) production polish matters less than story.

### Sections (in order)
1. **Hero** — Sharp, opinionated headline. Subhead naming the exact audience. Single primary CTA. Hero video > static image if available (Pro Max anti-pattern: "no video" for micro SaaS).
2. **Problem statement** — One scrolling section that puts the pain in the user's words. Scroll-triggered animation acceptable here.
3. **Solution overview** — How the product solves it, in 2-3 sentences. Optional product GIF or short loop.
4. **Key features** — 3-4 max. Resist scope-bloat — micro SaaS wins by being narrow.
5. **Founder/about strip** — Photo + 1-2 lines on why this exists. Builds trust where corporate logos can't.
6. **Social proof** — Tweets, screenshots of user feedback, indie-style. Less polished is more credible here.
7. **Pricing** — Usually one or two simple tiers. Lifetime deal optional.
8. **Final CTA** — Same as hero.

### Components needed
- Hero with video background or large product visual + single CTA
- Scroll-triggered text reveal blocks
- Founder card (photo + bio snippet)
- Tweet/screenshot embed component
- Simple pricing card (1-2 tiers)

### Anti-patterns
- Static design with no motion — feels lifeless for this category
- No video or product demo — high-engagement format expected
- Poor mobile experience — micro SaaS audiences skew mobile-first browsing
- Too many features — undermines the "small and focused" promise
- Corporate-style enterprise testimonials — wrong vibe entirely

### Cross-references
- DESIGN_SYSTEM.md → Video hero treatment, motion tokens, founder card
- ARCHITECTURE.md → Signup flow, payment processor

---

## Fintech/Crypto

**Conversion model:** Authority-driven trust, regulatory transparency, lower-friction signup with KYC handoff
**Primary CTA:** Open account / Start trading / Get started (depends on subcategory)
**Pattern:** Trust & Authority
**Style direction:** Minimalism + Accessible & Ethical, Navy + Trust Blue + Gold, professional and trustworthy typography

Fintech is one of the few industries where boring is correct. Trust signals dominate every other consideration.

### Sections (in order)
1. **Hero** — Mission-led headline. Often a stat ("$X billion processed") or capability ("Trade 100+ assets"). Less about feature, more about credibility.
2. **Trust strip** — Regulatory badges, licenses, security certifications, partner logos (banks, exchanges, custodians). Critical above the fold or just below.
3. **Product overview** — What the platform does, in plain language. Avoid crypto jargon if audience is broad.
4. **Key features or asset list** — Depends on subcategory. Trading platform = asset list; banking = features; payments = use cases.
5. **Security and compliance section** — Cold storage, audits, insurance, KYC/AML approach. Dedicated section, not a footer afterthought.
6. **Pricing or fees** — Transparent fee schedule. Hidden fees are a top anti-pattern.
7. **Social proof / press** — Coverage logos (TechCrunch, Bloomberg) work better than user testimonials in this space.
8. **Final CTA + secondary contact-sales path** — Self-serve and enterprise paths separated.
9. **Comprehensive footer** — Required disclosures, regulatory info, jurisdictional limits.

### Components needed
- Hero with stat or capability headline
- Trust badge strip (regulatory + security certs)
- Asset/feature list with icons
- Fee comparison table
- Press logo strip
- Compliance disclosure component (footer)

### Anti-patterns
- Playful design or whimsical illustration — actively undermines trust
- Unclear or hidden fees — top conversion killer in this space
- AI purple/pink gradients — reads as crypto-bro or unserious
- Dark mode by default for general audience (acceptable for traders)
- Buried regulatory information

### Cross-references
- DESIGN_SYSTEM.md → Trust badges, fee tables, regulatory disclosures, security state indicators
- ARCHITECTURE.md → KYC integration, payment rails, custodian/exchange APIs, compliance logging
- For a fintech mobile app, see `ux-ios` for screen-level skeletons

---

## AI/Chatbot Platform

**Conversion model:** Demo-driven — let people try it on the homepage
**Primary CTA:** Try it now (inline interactive demo) → Sign up for full access
**Pattern:** Interactive Demo + Minimal
**Style direction:** AI-Native UI + Minimalism, neutral palette + AI Purple (#6366F1) accent, modern clear typography

The defining characteristic of this category: the product is the demo. Anything that delays the user from typing into a prompt and seeing output is friction.

### Sections (in order)
1. **Hero with interactive prompt** — Input field is the hero CTA. User types, sees streaming output below. No signup required for first interaction.
2. **Sample prompts / use cases** — 3-5 quick-tap example prompts to lower the cold-start problem.
3. **How it works** — Brief explainer of the model, training data, or unique capability. Build credibility without overclaiming.
4. **Capabilities grid** — What the model can do, with examples. Show, don't tell.
5. **Integration / API section** — For developer-facing AI tools. Code snippet, language support, latency claims.
6. **Pricing** — Usually usage-based (per token, per request, per month). Show free tier prominently.
7. **Trust signals** — Customer logos, SOC2/HIPAA if applicable, model lineage (built on X, fine-tuned for Y).
8. **Final CTA** — Sign up for full access / Get API key.

### Components needed
- Interactive prompt input with streaming response display
- Sample prompt chips (one-tap to populate input)
- Capability card with example input/output
- Code snippet block (for API products)
- Usage-based pricing calculator
- Trust badge strip

### Anti-patterns
- Heavy chrome — competes with the demo for attention
- Slow response feedback — streaming text or typing indicators are mandatory; silent latency feels broken
- Gating the demo behind signup — defeats the entire conversion model
- Overclaiming capabilities — leads to disappointment and churn
- Generic "AI" branding — Pro Max flags AI purple/pink gradients as overused; differentiate

### Cross-references
- DESIGN_SYSTEM.md → Streaming text component, prompt input, capability card, code block
- ARCHITECTURE.md → Model API integration, rate limiting, usage tracking, billing

---

## Developer Tool / IDE

**Conversion model:** Trust through documentation depth, free tier or open core
**Primary CTA:** Install / Get started (with code snippet) OR Read the docs
**Pattern:** Minimal + Documentation
**Style direction:** Dark Mode (OLED) + Minimalism, dark syntax theme + blue focus, monospace + functional typography

Developer audiences distrust marketing polish. The docs are the marketing site.

### Sections (in order)
1. **Hero with install snippet** — One-line install command (npm install, brew install, curl ... | sh) prominently displayed. Above the fold. CTA is "copy this command."
2. **Quickstart code example** — 5-15 lines showing the simplest meaningful use. Copyable.
3. **Why use this** — Brief positioning vs alternatives. Honest about tradeoffs.
4. **Capabilities / features** — Code-snippet-driven. Each feature shown with the actual API call.
5. **Documentation entry points** — Links to guides, API reference, examples. Often displayed as a 3-card grid.
6. **Community / ecosystem** — Discord, GitHub stars, contributor count, integrations.
7. **Pricing (if commercial)** — Free tier prominent. Open source / paid split clearly explained.
8. **Final CTA** — Install command repeat + docs link.

### Components needed
- Install command block (with copy button)
- Code snippet block with syntax highlighting
- Documentation card (icon + title + 1-line description)
- GitHub repo embed or stats badge
- Pricing tier card (with "free" tier emphasized)

### Anti-patterns
- Light mode default — wrong audience entirely; default to dark
- Slow page performance — devs run lighthouse on tools they evaluate
- Stock illustrations of generic people coding — instant credibility loss
- Marketing-heavy copy ("revolutionary," "game-changing") — read as low-trust
- No documentation depth — devs evaluate based on docs quality, not landing page

### Cross-references
- DESIGN_SYSTEM.md → Code blocks, syntax highlighting tokens, install snippet, documentation cards
- ARCHITECTURE.md → Package distribution (npm, cargo, brew), telemetry opt-in, license server (if commercial)

---

## Marketing Agency

**Conversion model:** Portfolio-driven authority, results metrics, contact-form conversion
**Primary CTA:** Start a project / Get a quote / Let's talk
**Pattern:** Storytelling + Feature-Rich
**Style direction:** Brutalism + Motion-Driven, bold brand colors with creative freedom, expressive typography

The agency site IS the portfolio piece. Boring design is the worst possible signal.

### Sections (in order)
1. **Hero** — Bold statement of identity. Often kinetic typography or unconventional layout. Should feel like a portfolio piece in itself.
2. **Selected work** — 4-8 case studies as a grid or scroll-driven sequence. Each with project name, client, one-line outcome.
3. **Capabilities / services** — What the agency does. Group by service type (brand, web, motion) or by outcome (launch, scale, rebrand).
4. **Process** — How the agency works. Builds trust for the engagement model.
5. **Results / metrics** — Quantitative outcomes from past work ("3x revenue in 6 months"). Critical anti-pattern is hiding the results.
6. **Team** — Founders or key people. Builds personality.
7. **Client logos** — Below the fold. Not the lead with — work matters more than client list for agencies.
8. **CTA** — Contact form or "start a project" flow. Often a multi-step form gathering project type, budget, timeline.

### Components needed
- Kinetic typography hero
- Case study card with hover/scroll-triggered detail reveal
- Service card with bold visual treatment
- Process timeline/stepper
- Stat counter component (animated number)
- Multi-step contact form

### Anti-patterns
- Boring or template-y design — kills conversion immediately for this category
- Hidden work or vague case studies — trust killer
- Generic stock photography
- Over-claiming without metrics
- Hiding pricing tier signals (luxury vs accessible)

### Cross-references
- DESIGN_SYSTEM.md → Kinetic type, case study card, process stepper, stat counter
- ARCHITECTURE.md → CMS integration (case studies), contact form backend, lead routing

---

# Appendix: Compressed industry index

For industries without full sections above, here are one-line entries adapted from Pro Max's reasoning rules. Use as research seeds for tier 3 of the `UX.md` resolution chain — the skill should expand the entry by researching best practices, then offer to promote the result to a full section.

App-only categories (habit tracker, meditation, mood tracker, productivity tool, sleep tracker, etc.) are intentionally omitted here — those live in `ux-app` / `ux-ios` reference files.

| Industry | Pattern | Anti-patterns |
|---|---|---|
| E-commerce | Feature-Rich Showcase | Flat design without depth + Text-heavy pages |
| E-commerce Luxury | Feature-Rich Showcase | Vibrant & Block-based + Playful colors |
| B2B Service | Feature-Rich Showcase + Trust | Playful design + Hidden credentials + AI purple/pink gradients |
| Healthcare App | Trust + Clear CTA | Playful colors + Gamification |
| Educational App | Storytelling + Demo | Boring design + Cluttered UI |
| Creative Agency | Storytelling + Portfolio | Conservative design + No personality |
| Portfolio/Personal | Hero-Centric + Storytelling | Generic templates + Overproduced |
| Gaming | Visual-First + Demo | Conservative design + Static |
| Government/Public Service | Accessible + Trust | Trendy design + Unclear navigation + AI purple/pink gradients |
| Design System/Component Library | Documentation + Showcase | Decorative design + Hidden examples |
| NFT/Web3 Platform | Trust + Showcase | Unclear ownership + No verification |
| Creator Economy Platform | Storytelling + Social Proof | Hidden creators + Complex onboarding |
| Remote Work/Collaboration Tool | Interactive Demo + Feature-Rich | Static screenshots + No real-time feel |
| Mental Health App (marketing site) | Storytelling + Trust | Clinical design + Stigmatizing language |
| Pet Tech App (marketing site) | Visual-First + Storytelling | Generic design + No pet personality |
| Smart Home/IoT (marketing site) | Real-Time Monitoring | Static UI + No status indicators |
| EV/Charging Ecosystem | Real-Time + Map-First | Static map + Outdated charging info |
| Subscription Box Service | Storytelling + Social Proof | Generic packaging shots + No unboxing |
| Podcast Platform (marketing site) | Audio-First + Discovery | Text-heavy + Poor playback |
| Dating App (marketing site) | Visual-First + Social Proof | Stock photos + Inauthentic profiles |
| Micro-Credentials/Badges Platform | Trust + Showcase | Unclear value + No employer recognition |
| Knowledge Base/Documentation | FAQ/Documentation Landing | Cluttered nav + Poor search |
| Hyperlocal Services | Map-First + Trust | Generic listings + No local proof |
| Beauty/Spa/Wellness Service | Hero-Centric + Social Proof | Bright neon + Harsh animations + Dark mode + AI purple/pink gradients |
| Luxury/Premium Brand | Hero-Centric + Editorial | Cluttered design + Cheap-looking effects |
| Restaurant/Food Service | Visual-First Menu | Stock photos + Slow loading |
| Real Estate/Property | Visual-First + Map | Poor photos + Cluttered listings |
| Travel/Tourism Agency | Visual-First + Storytelling | Generic stock + No destination feel |
| Hotel/Hospitality | Visual-First + Booking | Poor photos + Hidden pricing |
| Wedding/Event Planning | Visual-First + Storytelling | Generic templates + Stock photos |
| Legal Services | Trust + Authority | Playful design + Vague credentials |
| Insurance Platform | Trust + Clear Comparison | Hidden terms + Confusing pricing |
| Banking/Traditional Finance | Trust & Authority | Trendy design + AI purple/pink gradients |
| Online Course/E-learning | Demo + Social Proof | Generic UI + No instructor presence |
| Non-profit/Charity | Storytelling + Social Proof | Sales-y tone + Hidden impact |
| Music Streaming (marketing site) | Audio-First + Discovery | Cluttered UI + Poor playback |
| Video Streaming/OTT (marketing site) | Visual-First + Discovery | Slow loading + Poor categorization |
| Job Board/Recruitment | Search-First + Filters | Cluttered listings + No salary info |
| Marketplace (P2P, marketing site) | Marketplace / Directory | Unclear trust signals + No reviews |
| Logistics/Delivery (marketing site) | Real-Time + Map | Static tracking + Vague ETAs |
| Agriculture/Farm Tech | Data-Dense + Real-Time | Generic UI + No field-context |
| Construction/Architecture | Visual-First + Portfolio | Poor project photos + Hidden specs |
| Automotive/Car Dealership | Visual-First + Comparison | Stock photos + Hidden pricing |
| Photography Studio | Visual-First + Portfolio | Slow gallery + Watermark overload |
| Coworking Space | Visual-First + Booking | Poor photos + Hidden pricing |
| Home Services (Plumber/Electrician) | Trust + Local Proof | No reviews + Vague pricing |
| Childcare/Daycare | Trust + Visual | Stock photos + Vague credentials |
| Senior Care/Elderly | Accessible + Trust | Small text + Trendy design |
| Medical Clinic | Trust + Booking | Cluttered + Stock medical photos |
| Pharmacy/Drug Store | Trust + Clear Catalog | Cluttered UI + Hidden prices |
| Dental Practice | Trust + Visual | Generic stock + Vague credentials |
| Veterinary Clinic | Trust + Warm Visual | Clinical/cold tone + Stock photos |
| Florist/Plant Shop | Visual-First + Catalog | Poor product photos + No seasonality |
| Bakery/Cafe | Visual-First + Local | Generic stock + Hidden hours |
| Brewery/Winery | Storytelling + Visual | Generic templates + No origin story |
| Airline | Search-First + Trust | Hidden fees + Poor mobile booking |
| News/Media Platform | Editorial Grid | Cluttered ads + Poor scanability |
| Magazine/Blog | Editorial Grid + Storytelling | Cluttered + Aggressive popups |
| Freelancer Platform | Trust + Showcase | Vague portfolios + No reviews |
| Event Management | Visual-First + Booking | Generic templates + Hidden pricing |
| Membership/Community | Social Proof + Storytelling | Hidden member benefits + Stale activity |
| Newsletter Platform | Newsletter / Content First | Multi-field forms + No sample content |
| Digital Products/Downloads | Showcase + Social Proof | Generic templates + No previews |
| Church/Religious Organization | Storytelling + Community | Outdated UI + Hidden service times |
| Sports Team/Club | Visual-First + Social Proof | Static UI + No real-time scores |
| Museum/Gallery | Visual-First + Storytelling | Poor image quality + Cluttered UI |
| Theater/Cinema | Visual-First + Booking | Hidden showtimes + Slow seat selection |
| Coding Bootcamp | Trust + Social Proof | Vague outcomes + Hidden pricing |
| Cybersecurity Platform | Trust + Real-Time | Marketing fluff + No technical depth |
| Biotech / Life Sciences | Trust + Authority | Marketing-heavy + Vague science |
| Space Tech / Aerospace | Trust + Visual | Sci-fi cosplay + Vague capabilities |
| Architecture / Interior | Visual-First + Portfolio | Poor photos + Generic templates |
| Quantum Computing Interface | Trust + Documentation | Marketing fluff + No technical depth |
| Sustainable Energy / Climate Tech | Trust + Storytelling | Greenwashing tone + Vague impact |
| Generative Art Platform (marketing site) | Visual-First + Demo | Slow generation + Poor preview |
| Spatial Computing OS / App (marketing site) | Visual-First + Demo | Flat 2D screenshots + No spatial context |
| Anonymous Community / Confession (marketing site) | Storytelling + Privacy | Identity exposure + Hostile tone |
| Local Events & Discovery (marketing site) | Map-First + Visual | Generic listings + Hidden details |
| VPN & Privacy Tool (marketing site) | Trust + Minimal | Cluttered UI + Vague privacy claims |
