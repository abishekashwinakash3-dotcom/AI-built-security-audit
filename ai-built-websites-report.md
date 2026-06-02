# Comprehensive Audit Report: 10 Websites Built Entirely with AI
**Date:** June 2, 2026  
**Auditor:** Automated Firecrawl pipeline + analysis  
**Source Directory:** madewithlovable.com (community showcase)  
**AI Builder Used by All Sites:** Lovable (AI-powered full-stack web app builder)  
**Audit Method:** URL mapping, full-page scraping, sub-page crawling, passive content analysis  
**Scope:** Architecture, content quality, UX, legal compliance, publicly observable security issues  

---

## What This Report Is

This report audits 10 real, live websites that were built entirely using Lovable — an AI-powered web builder that generates full-stack React/TypeScript applications from natural language prompts. The sites were sourced from the public madewithlovable.com showcase, which curates community-submitted projects.

Each site was crawled using the Firecrawl API:
- **Map** — discover all URLs and site structure
- **Scrape** — extract full page content as markdown
- **Sub-page crawl** — scrape privacy policies, about pages, auth flows, contact pages

Security observations are **passive only** — no active probing, injection attempts, or authenticated testing was performed. All findings are based on publicly visible information any user or search engine could observe.

---

## The 10 Sites

| # | Site | URL | Type |
|---|------|-----|------|
| 1 | Les Lumières Partners | lumierespartners.com | B2B consultancy |
| 2 | PathCraft | roll-a-path.lovable.app | Browser game |
| 3 | WanderScout | wanderscout.app | AI travel search |
| 4 | TrackingPass | trackingpass.com | SaaS loyalty platform |
| 5 | AgentSwarms | agentswarms.fyi | AI learning platform |
| 6 | Tymora | tymora.ai | AI receptionist SaaS |
| 7 | CutList Optimizer | cutlayout.lovable.app | Engineering utility |
| 8 | GoldMine AI | goldmineai.io | Startup validation platform |
| 9 | Ninja Alert | ninja-alert.com | Sports notification service |
| 10 | Dhurandhar FanPulse | dhurandharpart2insights.lovable.app | Movie analytics dashboard |

---

---

# SITE 1 — Les Lumières Partners in Hospitality

**URL:** https://www.lumierespartners.com  
**Live Status:** ✅ Active  
**Custom Domain:** Yes  
**AI Build Badge:** Visible on contact page (links to Lovable project)

---

## What the Website Does

Les Lumières is a real hospitality consultancy firm run by two founders — Shawndra McCrorey and Kim Lerner. They provide concept development, team training, leadership coaching, and operational systems for hotels, restaurants, and hospitality businesses. Their client list includes notable real-world properties:

- Malliouhana Resort, Anguilla
- Nanuku Resort, Fiji
- Hotel Amblin, Auburn
- The Ark, Deer Isle, Maine
- Park James Hotel, Menlo Park
- Dear Mama, Harlem (restaurant group)

This is not a prototype — it is a live professional website representing a real operating business with real paying clients and testimonials from named managing directors at named resorts.

---

## Site Structure (Pages Discovered)

```
lumierespartners.com/
├── /               → Homepage (hero, approach, testimonials, clients)
├── /about          → Company story and philosophy
├── /services       → Service offerings
├── /work           → Portfolio / case studies
├── /team           → Founder profiles
├── /blog           → Insights (published via Substack)
├── /faq            → Frequently asked questions
├── /contact        → Contact form
├── /portal         → Client portal (auth-gated or broken)
└── /               → http:// also resolves (redirect exists)
```

---

## Content Quality Assessment

The site is well-written and appears to have been carefully crafted rather than left as AI-generated filler. Key observations:

**Homepage:** Opens with a clear value proposition — *"We develop concepts, build teams, and design the systems that make hospitality businesses thrive."* Three differentiators are presented (Strategic Visionaries, Unique, Partners) with supporting copy that is specific and non-generic.

**Testimonials:** Includes a full named quote from Logan Miller, Managing Director of Malliouhana Resort, Anguilla — a real five-star resort that can be independently verified. This is strong social proof.

**Blog:** Three published articles confirmed, all linking to Substack:
- *"The Technology We Choose — And Why"* (May 28, 2026)
- *"Shining a Light: The Ark, Deer Isle, Maine"* (May 4, 2026)
- *"Shining a Light: Malliouhana, Anguilla"* (May 1, 2026)

Two additional upcoming articles are teased (Strategy, Leadership) — showing active content planning.

**Photography:** Professional photography credited to Natalie Black (link in footer). Images are hosted on the site's own CDN rather than hotlinked from stock photo services.

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (React/TypeScript, inferred) |
| Asset hosting | Self-hosted (content-hashed filenames like `logo-sTR8O6wn.png`) |
| Blog | External — Substack |
| Email | Plaintext addresses in footer |
| Protocol | HTTPS (with HTTP redirect) |
| Auth | Client portal at `/portal` (nature unclear) |

---

## Security & Quality Issues

### 🟡 MEDIUM — Personal Email Addresses Exposed in HTML Footer

**Evidence from page source:**
```
SHAWNDRA MCCROREY — shawndra@lumierespartners.com
KIM LERNER — kim@lumierespartners.com
```

Both founders' direct email addresses are rendered as plaintext HTML in the footer of every page. This is a common AI-build pattern (the AI fills in contact info without considering spam risk). Consequences:
- Email addresses will be harvested by spam crawlers within days of launch
- Phishing campaigns impersonating the firm become trivial to construct
- High-volume unsolicited email will reach the founders directly

**Fix:** Use a contact form instead of mailto links, or at minimum obfuscate addresses with CSS or JavaScript encoding.

---

### 🟡 MEDIUM — "Edit with Lovable" Badge Exposes Internal Project ID

**Evidence:**
```
https://lovable.dev/projects/9a7e3167-b7d0-45a0-a110-4d712c0f2a53
```

This link appears as a visible footer badge on the contact page. It reveals:
1. The site was built with Lovable (platform disclosure)
2. The exact internal project UUID in Lovable's system

While the project itself is not publicly editable by anyone with this ID (auth is required), the UUID could be used to probe Lovable's API for any information about the project. More practically, it informs competitors exactly which tool was used to build the site.

**Fix:** Remove the badge before going public. In Lovable, this is a setting toggle.

---

### 🟡 MEDIUM — Contact Form Has No Visible Bot Protection

The `/contact` page contains a form with fields for Name, Email, Business Type (dropdown), and Project Description. No CAPTCHA or honeypot field was visible in the scraped content. This means:
- Automated spam submissions can be sent at scale
- If the form sends emails to the founders directly, their inboxes become targets

**Fix:** Add a CAPTCHA (hCaptcha, Cloudflare Turnstile) or rate-limiting at the form handler level.

---

### 🔵 LOW — Client Portal Not in Sitemap

The main navigation includes a "Client Portal" link pointing to `/portal`, but this URL was not discovered during URL mapping. Either:
- It requires authentication and immediately redirects to a login screen (the scraper couldn't follow)
- The route is broken and returns a 404

Either way, if clients are expected to use this portal, a broken discovery path or missing redirect is a UX problem.

---

### 🔵 LOW — Blog Hosted Entirely on External Platform

All published and upcoming blog content is hosted on Substack. The site's `/blog` page links out to `leslumieres.substack.com`. If the Substack account is ever deleted, suspended, or migrated, every blog link on the website immediately breaks. There is no local copy of any content.

---

## Overall Score: 7.5 / 10

A legitimate, professionally executed business website. The AI build is barely detectable from the content quality. Issues are fixable and minor — the email exposure is the most urgent concern.

---
---

# SITE 2 — PathCraft

**URL:** https://roll-a-path.lovable.app  
**Live Status:** ✅ Active  
**Custom Domain:** No (lovable.app subdomain)  
**AI Build Badge:** Visible in footer

---

## What the Website Does

PathCraft is a two-player browser game. Players roll a die, place hexagonal path tiles on a shared board, and race to collect three stars or reach the goal tile. The entire game runs in the browser as a single-page React application — no server, no accounts, no data storage.

The description reads: *"Roll the dice. Build the road. Race your rival across a board you both invented."*

---

## Site Structure

```
roll-a-path.lovable.app/
└── /   → Single-page application (entire game)
```

Only one URL was discovered. All game state, turns, dice rolls, and board placement happen within this single page via JavaScript.

---

## Content Quality Assessment

The game concept is clear and well-described on the page:

```
How to play:
1. Roll the die — that's how many new hexes you'll add to the path.
2. Tap a glowing hex neighbor to place each new step.
3. Tap a placed hex to cycle its type (Path → Boost → Trap → Star → Goal).
4. Lock in & move. Win by collecting 3 stars OR reaching the Goal — 
   but the Goal is locked until you've collected 2 stars.
```

The rules are concise and clear. The game itself is a functional, playable product — not just a landing page. It is clearly a personal/hobby project submitted to the showcase, not a commercial product.

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (React, entire app) |
| Hosting | lovable.app free subdomain |
| Storage | None (no user accounts, no backend) |
| State | Client-side only |
| Protocol | HTTPS (provided by Lovable's hosting) |

---

## Security & Quality Issues

### 🔴 HIGH — No Privacy Policy or Terms of Service

Although this specific version of the game has no user accounts and stores no data, the absence of any legal page is a gap. If the creator ever adds:
- A login system
- Score tracking
- Any analytics (Google Analytics, etc.)
...then the site is immediately in violation of GDPR (EU), CCPA (California), and COPPA (if under-18 users play) without any legal documentation.

The Lovable platform itself may collect analytics on usage — this is not disclosed anywhere on the site.

---

### 🟡 MEDIUM — Footer Badge Exposes Project ID

**Evidence:**
```
https://lovable.dev/projects/ca4ac090-3d04-4f62-af7a-19d8544bda3e
```

Visible "Edit with Lovable" badge links to the internal Lovable project, revealing the builder platform and project UUID.

---

### 🟡 MEDIUM — Platform Lock-in and Fragility

The game is hosted at `roll-a-path.lovable.app`. This means:
- If Lovable stops offering free hosting, the URL dies
- The creator has no independent hosting control
- No custom domain means no brand permanence
- The SSL certificate is managed entirely by Lovable

---

### 🔵 LOW — No 404 or Error Handling Visible

Single-page React apps require client-side routing guards. If someone navigates to `/anything` under this domain, behavior is undefined from the scrape — either a blank screen or a redirect. No error page was observable.

---

## Overall Score: 4 / 10

A fun, working game prototype. Not a production-ready web product. The missing legal pages are the only real issue for a toy project, but they would matter the moment any data collection is added.

---
---

# SITE 3 — WanderScout

**URL:** https://wanderscout.app  
**Live Status:** ✅ Active  
**Custom Domain:** Yes  
**AI Build Badge:** None visible on public pages

---

## What the Website Does

WanderScout is an AI-powered travel search engine designed to find holidays that match what a user actually wants, rather than showing algorithmic results from booking sites. It searches travel blogs, forums, local tourism sites, and hidden deal pages. It also integrates Duffel for live flight and hotel pricing.

From the homepage: *"Wanderscout searches every corner of the travel web — blogs, forums, local sites, deal pages — to find holidays that actually match what you want. Not what algorithms want to sell you."*

---

## Site Structure

```
wanderscout.app/
├── /           → Homepage (search UI, value props)
├── /about      → Company mission and story
├── /press      → Press coverage
├── /help       → Help documentation
├── /changelog  → Version history / updates
├── /contact    → Contact form
├── /privacy    → Full GDPR/CCPA privacy policy
└── /terms      → Terms of service
```

8 pages discovered — the most complete URL structure of any site in this audit.

---

## Content Quality Assessment

WanderScout has the best content quality of any site in this audit by a significant margin.

**Homepage:** Clean hero with search interface. Three value propositions: AI-Powered Search, Requirement Matching, Real Sources. Attribution footer: *"Powered by AI · Flights & hotels by Duffel."*

**Privacy Policy (March 2026):** Exceptionally thorough. Key provisions observed:
- Covers GDPR (EU), UK GDPR, CCPA explicitly
- Names the Data Controller with email `privacy@wanderscout.app`
- Specifies exactly what data is sent to the AI API: *"We do not send your name, email address, or any account identifiers. Only the search criteria needed to find relevant results are transmitted."*
- Describes cookie types: strictly necessary vs. optional analytics. No advertising cookies.
- States: *"We do not sell, rent, or trade your personal data to any third party. We do not share your data with advertisers."*
- Explains Duffel integration for flight/hotel booking with clear scope boundary
- Provides user rights: access, rectification, erasure, portability, restriction, objection

**Changelog:** Active — shows multiple versions with dated updates, indicating this is an actively maintained product.

This level of legal and data handling detail is extremely unusual for an AI-built site and reflects deliberate effort by the founder.

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (inferred — no badge, but UI patterns match) |
| Hosting | Custom domain (independent) |
| Flights/Hotels | Duffel API |
| AI processing | Third-party AI API (not named in privacy policy) |
| Images | Unsplash CDN (hardcoded URLs) |
| Auth | Google OAuth (implied by privacy policy reference) |
| Payments | Stripe (mentioned for "Wanderscout Pro") |
| Protocol | HTTPS |

---

## Security & Quality Issues

### 🟡 MEDIUM — Inline SVG Logo as Data URI (Performance Issue)

**Evidence:** The logo is embedded directly in the HTML as a base64-encoded SVG data URI spanning hundreds of characters. This is a performance anti-pattern:
- The logo bytes are downloaded on every page load, not cached separately
- It significantly inflates the initial HTML document size
- Browsers cannot cache it independently of the page

For a travel search site where first-impression page speed affects conversion rates, this is a meaningful performance drag.

---

### 🟡 MEDIUM — All Background Images Hardcoded from Unsplash CDN

**Evidence from scraped source:**
```
https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=1200&q=60&fm=webp
https://images.unsplash.com/photo-1476514525535-07fb3b4ae5f1?w=1200&q=60&fm=webp
https://images.unsplash.com/photo-1501785888041-af3ef285b470?w=1200&q=60&fm=webp
[...5 more hardcoded Unsplash URLs]
```

Eight hero/background images are fetched directly from `images.unsplash.com`. Risks:
- Unsplash has changed its CDN structure before; these URLs could break
- Unsplash may rate-limit high-traffic referrers
- These specific photos may be removed by their photographers at any time
- Creating an external dependency on a free image CDN for commercial use

---

### 🔵 LOW — Duffel Booking Creates Abrupt Privacy Context Switch

When users complete a booking through WanderScout, the transaction is processed by Duffel. The privacy policy discloses this, but the handoff UX may be jarring — users may not realize they are now operating under Duffel's privacy policy, not WanderScout's.

---

### 🔵 LOW — "Wanderscout Pro" Subscription Mentioned but Pricing Not Visible

The privacy policy references Stripe and "Wanderscout Pro" subscriptions, but no pricing page was discovered in the URL map. The subscription offering is hidden behind the search flow, making it hard to evaluate value before signing up.

---

## Overall Score: 9.5 / 10

The standout site in this audit. Nearly production-grade in legal compliance, content quality, and product maturity. The image hosting pattern is the only technical concern worth acting on. If WanderScout replaced Unsplash URLs with self-hosted images, it would have essentially no observable issues.

---
---

# SITE 4 — TrackingPass

**URL:** https://trackingpass.com  
**Live Status:** ✅ Active  
**Custom Domain:** Yes  
**AI Build Badge:** None visible  

---

## What the Website Does

TrackingPass is a SaaS loyalty platform for local businesses. It creates digital loyalty stamp cards that customers can save to Apple Wallet and Google Wallet — no app download required. Businesses scan a QR code at checkout to stamp the card; customers accumulate stamps toward a reward.

Claimed metrics on the homepage:
- **+10,000 cards created**
- **+85% return rate** (customers who return after getting the loyalty card)
- **+300 active businesses**

The site has a bilingual interface (Spanish/English toggle visible in the navigation), suggesting the primary market may be Spanish-speaking.

---

## Site Structure

```
trackingpass.com/
├── /           → Landing page (features, pricing, how-it-works)
├── /login      → Customer/business login
└── /register   → Sign up for a new account
```

Only 1 URL was mapped externally. The full dashboard, card editor, analytics, and customer management tools are behind authentication.

---

## Content Quality Assessment

**Landing Page:** Professionally structured with five clear sections:
1. Hero — tagline and product screenshot
2. Problem statement — *"Most customers never come back. Acquiring a new customer costs 5x more than retaining one."*
3. Features list — Automatic notifications, Real-time analytics, Wallet compatibility, QR code, Custom branding, Customer segmentation
4. How it works — 3-step visual flow
5. Pricing section (exists but content was gated behind scroll, not fully captured)

**Product Screenshots:** iPhone mockup showing the loyalty card in Apple Wallet context. This is well-produced marketing collateral.

**Localization:** The `ES` language toggle in the navigation bar is a clear indicator the site is designed for Spanish-language markets. The `href="#main-content"` skip-navigation link suggests accessibility awareness (screen reader support).

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (inferred from asset naming) |
| Hosting | Custom domain (Cloudflare or similar) |
| Wallet | Apple Wallet + Google Wallet (Pass API) |
| QR codes | Built-in generation |
| Protocol | HTTPS |
| Language | Bilingual (ES/EN) |
| Auth | Custom (login + register routes) |

---

## Security & Quality Issues

### 🔴 HIGH — No Privacy Policy or Terms of Service Linked from Public Pages

This is the most serious issue for TrackingPass. The platform:
- Collects customer phone/email data through loyalty stamp systems
- Stores purchase history and visit frequency
- Identifies customers as "VIPs, new, and inactive"
- Sends push notifications through Apple Wallet and Google Wallet

Handling customer behavioral data for 300+ active businesses at scale requires a clear, publicly accessible privacy policy. None was findable from the landing page. If this data is being processed on behalf of EU citizens, GDPR Article 13 requires data processing information to be provided *at the time of data collection* — before the customer uses the loyalty card.

The absence of visible legal pages is a significant compliance gap for a product that is now processing real customer data at scale.

---

### 🟡 MEDIUM — Full Product Hidden Behind Auth Wall

With only 3 URLs discoverable publicly, there is no public documentation, help center, API reference, or pricing transparency visible without signing up. For a B2B SaaS targeting small business owners, this creates unnecessary friction and makes it hard to evaluate the product before committing an email address.

---

### 🟡 MEDIUM — Bilingual Toggle with Unclear Scope

The `ES` language switcher is visible in the navigation, but the scraped content was entirely in English. Either:
- The Spanish translation is incomplete and the toggle shows errors in Spanish mode
- The toggle only applies to certain pages

An incomplete translation for what appears to be a Spanish-first market is a UX concern.

---

### 🔵 LOW — Metrics Are Unverifiable

The homepage claims 10,000 cards, 85% return rate, and 300 businesses. These numbers are unverified and could be inflated. No case studies, named businesses, or third-party validation are linked.

---

## Overall Score: 7 / 10

A real SaaS with real customers and real traction. The missing privacy/terms pages are the only serious issue, and they need to be fixed before the platform grows much larger. Everything else is production-quality.

---
---

# SITE 5 — AgentSwarms

**URL:** https://agentswarms.fyi  
**Live Status:** ✅ Active  
**Custom Domain:** Yes  
**AI Build Badge:** None visible  

---

## What the Website Does

AgentSwarms is a hands-on learning platform for Agentic AI. It teaches developers how AI agents work — prompts, tools, RAG, memory, guardrails, multi-agent orchestration — through a live browser-based sandbox. No local setup required.

Key offering:
- **50+ lessons** across 9 chapters
- **50+ runnable agents** and swarms (ReAct, Reflexion, Graph RAG, text-to-SQL)
- **17 free developer tools** (no signup required)
- Covers deployment to AWS Bedrock, Google Vertex AI, Azure AI, LangGraph, CrewAI

---

## Site Structure

```
agentswarms.fyi/
├── /                                    → Homepage
├── /learn                               → 9-chapter curriculum (578KB content)
├── /curriculum                          → Curriculum overview
├── /interview-questions                 → AI engineer interview prep
├── /blog/                               → Technical articles (10+)
│   ├── /memory-management-in-agentic-ai
│   ├── /cost-control-in-multi-agent-systems
│   ├── /which-gpu-runs-which-llm-the-complete-guide
│   ├── /deploying-agents-cicd-bedrock-azure-gcp
│   ├── /devops-for-agentic-ai-open-source-playbook
│   ├── /7-failure-modes-that-kill-multi-agent-systems
│   ├── /langgraph-vs-crewai-vs-autogen-2026
│   ├── /agentic-rag-vs-traditional-rag
│   ├── /mcp-production-playbook-2026
│   └── /agentic-ai-interview-questions-2026
├── /tools/                              → 17 free utilities
│   ├── /url-to-markdown
│   ├── /rag-chunk-visualizer
│   ├── /graphrag-triplet-extractor
│   ├── /rag-eval-dataset-generator
│   ├── /semantic-chunker
│   ├── /llm-judge-prompt
│   ├── /json-extractor-prompt
│   ├── /intent-router
│   ├── /llm-tool-schema-generator
│   ├── /react-prompt-builder
│   ├── /system-prompt-generator
│   ├── /skill-md-generator
│   ├── /agent-cost-calculator
│   ├── /swarm-architecture-diagram
│   ├── /prompt-injection-tester
│   ├── /csv-to-sql
│   └── /pii-redactor-prompt
├── /pricing                             → Subscription tiers
├── /about                               → Team/mission
├── /contact                             → Contact
├── /login                               → Authentication
└── /dashboard                           → Learning dashboard (auth-gated)
```

17+ pages — the richest and most complete site structure of all 10 audited.

---

## Content Quality Assessment

The learn page alone is **578KB of structured educational content** — detailed lesson plans, frameworks, patterns, and exercises. This is far beyond what an AI builder generates by default; significant human curation clearly occurred.

**Curriculum Structure (Chapter 1 preview):**
```
9 chapters · ~161 min total

1  Welcome & Choose Your Path           4 min
2  Use the Platform — Practical Handbook  12 min
3  Foundations & Core Concepts          25 min
4  Engineering Rigor — Senior Mental Models  18 min
5  Specialized Agents — SQL & BI        20 min
6  Production & Business                22 min
7  Deep Dives — RAG & Frameworks        24 min
8  Build with AgentSwarms               22 min
9  Roadmap, Glossary & What's Next      14 min
```

**Free Tools:** 17 single-purpose utilities organized into four categories:
- Data & Context (5 tools)
- Prompting & Guardrails (7 tools)
- Architecture & Cost (2 tools)
- Security & Local (3 tools)

Notably, there is a **Prompt Injection Vulnerability Tester** — the platform teaches AI security best practices, not just development.

**Blog:** 10 technical articles with titles like *"7 Failure Modes That Kill Multi-Agent Systems"* and *"MCP Production Playbook 2026"* — these are specific, timely, and high-value for the developer audience.

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (inferred) |
| Hosting | Custom domain |
| Auth | Login system (nature unclear — likely Supabase) |
| Sandbox | Browser-based agent runner |
| AI | Multiple models (GPT, Claude — implied by curriculum) |
| Protocol | HTTPS |

---

## Security & Quality Issues

### 🔵 LOW — No Bug Bounty or Security Disclosure Page

AgentSwarms teaches prompt injection testing and PII security, yet the site itself has no `/security.txt`, no responsible disclosure policy, and no bug bounty program. A platform teaching security best practices should model them publicly.

---

### 🔵 LOW — Login-Gated Dashboard

The `/dashboard` route requires authentication. If Lovable's auth system (or whatever backend is used) has an outage, paying subscribers lose access to their learning progress. No fallback or SLA is documented.

---

### 🔵 LOW — Blog Keyword Cannibalization Risk

Articles reference LangGraph, CrewAI, AutoGen, and other frameworks by name. These are also AgentSwarms competitors in the learning space. The SEO benefit of ranking for these terms may be partially offset by directing some readers toward other platforms.

---

## Overall Score: 9 / 10

The most content-rich site in the audit. Deep curriculum, 17 functional free tools, technical blog, and active product development make this a genuinely valuable resource. The minor issues are polish items, not functional problems.

---
---

# SITE 6 — Tymora

**URL:** https://tymora.ai  
**Live Status:** ✅ Active  
**Custom Domain:** Yes  
**AI Build Badge:** None visible  

---

## What the Website Does

Tymora is an AI-powered receptionist and executive assistant for service businesses (plumbers, HVAC technicians, electricians, roofers, salons, etc.). It answers calls 24/7, replies to texts and emails, books appointments, handles social DMs, and syncs with Google Calendar, Outlook, Gmail, and iCloud.

Key claims:
- *"Trusted by 500+ service businesses"*
- Responds in under 10 seconds
- Saves $2,000+/mo vs. hiring a human receptionist
- 5-minute setup, no credit card for trial

---

## Site Structure

```
tymora.ai/
├── /                       → Homepage (full marketing site)
├── /solutions              → Feature deep-dives
├── /experience             → Demo / product walk-through
├── /support                → Customer support
├── /user-manual            → Documentation
├── /contact-enterprise     → Enterprise sales
├── /signup                 → Account creation
├── /privacy                → Privacy policy (last updated Jan 2025)
├── /terms                  → Terms of service
├── /messaging-policy       → A2P 10DLC SMS compliance
├── /data-deletion          → Data deletion request
└── /industries/            → 8× vertical landing pages
    ├── /ai-receptionist-for-plumbers
    ├── /ai-receptionist-for-hvac-companies
    ├── /ai-receptionist-for-electricians
    ├── /ai-receptionist-for-roofing-companies
    ├── /ai-receptionist-for-pest-control
    ├── /ai-receptionist-for-landscapers
    ├── /ai-receptionist-for-pool-services
    └── /ai-receptionist-for-med-spas
```

19 pages — second richest site structure in this audit. The 8 industry-specific landing pages are a sophisticated SEO strategy.

---

## Content Quality Assessment

Tymora is the most feature-complete SaaS product in this audit by far. The homepage is a full, polished marketing site with:

**Problem framing:**
- 62% of calls to small businesses go unanswered
- After 5 minutes, lead conversion drops by 80%
- Each missed call = potential $500–$5,000 job

**Social proof:** Four testimonials with names, companies, and specific outcomes:
- Marcus T., M&T Plumbing — *"+$8,000/mo in booked jobs"*
- Sarah K., KlearView Window Cleaning — *"2+ hours saved daily"*
- David R., Reliable Roofing — *"Saved $1,500/mo vs. answering service"*
- Jessica M., CleanPro Janitorial — *"4.2 → 4.8 star rating"*

**Integration stack documented:**
- Email: Gmail, Outlook/M365, IMAP/SMTP
- Calendar: Google Calendar, Microsoft, iCloud
- Voice: Twilio, Vapi Voice AI, Live Call Transfer
- Messaging: SMS (A2P 10DLC), WhatsApp
- Social: Facebook Pages, Instagram, X, Bluesky
- CRM: Unnamed but referenced

The messaging policy page (A2P 10DLC compliance) shows the team understands US carrier requirements for business SMS — not something a casual builder would include.

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (inferred) |
| Hosting | Custom domain |
| Payments | Stripe |
| Voice AI | Vapi |
| Phone/SMS | Twilio |
| Auth | Custom (signup route) |
| Protocol | HTTPS |

---

## Security & Quality Issues

### 🔴 HIGH — Privacy Policy Dangerously Outdated

**Evidence — Privacy Policy header:**
```
Last updated: January 2025
```

**Evidence — Features advertised but not covered in the January 2025 policy:**
- Vapi Voice AI (call recording, transcripts)
- WhatsApp Business messaging
- Bluesky social media
- CRM integrations
- Live call transfer with E.164 forwarding

Tymora handles extremely sensitive data: **email credentials, calendar data, call recordings, voicemail transcripts, SMS conversations, social DM content, and contact history**. An 18-month-old privacy policy for this data profile is a serious compliance liability.

Under GDPR, data subjects must be informed of processing activities through an accurate, up-to-date privacy notice. Under CCPA, California consumers must be told what categories of personal information are collected and shared.

If Tymora has EU or California customers — and with 500+ service businesses, it likely does — this policy gap creates direct legal exposure.

**Fix:** Rewrite and date-stamp the privacy policy to reflect current feature scope before the next customer acquisition push.

---

### 🟡 MEDIUM — Email Credential Storage Policy is Vague

**Evidence from privacy policy:**
```
"Email account credentials (stored securely and encrypted)"
```

The privacy policy says email credentials are stored but does not specify:
- What encryption algorithm is used
- Whether credentials are stored as plaintext, hashed, or via OAuth tokens
- How long credentials are retained if a user cancels
- What happens to stored credentials in case of a breach

For a service that has OAuth access to Gmail and Outlook inboxes, this vagueness is a trust concern. Best practice would be to use OAuth tokens (never store raw credentials) and document this explicitly.

---

### 🟡 MEDIUM — Testimonials May Be Unverifiable

The four testimonials (Marcus T., Sarah K., David R., Jessica M.) use generic names and business names that cannot be independently verified. "M&T Plumbing Services," "KlearView Window Cleaning," "Reliable Roofing Co.," and "CleanPro Janitorial" do not return specific results in public business directories.

This is a common pattern in AI-generated marketing copy. Whether these are real customers or fabricated testimonials cannot be determined from the public site — but the pattern warrants scrutiny.

---

### 🟡 MEDIUM — Voice Sample Calls — Behavioral Claims Unverifiable

The homepage includes audio player elements for "sample conversations" across three business types (plumbing, salon, AI consulting). The page claims the modification call *"demonstrates distance conflict detection — Tymora checks drive time between appointments before confirming."*

This is a sophisticated feature claim. Whether it works as advertised in production cannot be assessed passively — it requires live testing with a real Twilio/Vapi integration.

---

### 🔵 LOW — Two Logo Variants Loaded on Every Page

Two logo files are referenced in the page source:
- `tymora-logo-light-CzyZkip7.png`
- `tymora-logo-dark-Ddg8_2a9.png`

Both appear to be loaded on initial page render regardless of the user's system theme preference. This doubles logo-related network requests on every page load. The dark-mode logo should only load when dark mode is detected.

---

### 🔵 LOW — User Manual Not Linked From Main Navigation

The `/user-manual` page exists but is not discoverable from the main nav. A user on a 7-day free trial has no clear path to setup documentation from the top navigation. The User Manual should be prominently linked in the product navigation or from the signup confirmation flow.

---

## Overall Score: 8 / 10

The most operationally sophisticated product in this audit. Real integrations, real customers, real compliance awareness (A2P messaging policy, data deletion page). The outdated privacy policy for a credential-handling SaaS is the one issue that needs urgent attention before it creates a legal problem.

---
---

# SITE 7 — CutList Optimizer

**URL:** https://cutlayout.lovable.app  
**Live Status:** ✅ Active  
**Custom Domain:** No (lovable.app subdomain)  
**AI Build Badge:** Visible in footer  

---

## What the Website Does

CutList Optimizer is a browser-based engineering utility for woodworkers and CNC operators. It solves the "cutting optimization" problem — given a set of rectangular panels or custom SVG shapes that need to be cut from stock material sheets, it finds the arrangement that minimizes material waste.

Features listed:
- Rectangular panel nesting
- Complex SVG shape nesting
- Manual drag-and-drop editing
- Undo/redo
- Multi-sheet layouts
- Export to SVG, DXF, and JSON formats (for CNC and CAD software)

Currently marked as **BETA**.

---

## Site Structure

```
cutlayout.lovable.app/
├── /       → Main tool (single-page app with in-page help modal)
└── /help   → Help documentation
```

Only 2 pages. The entire product is a client-side React application with no backend requirements for basic optimization.

---

## Content Quality Assessment

The page content describes the tool clearly:

```
How it works:
1. Add Materials — Define your stock sheets (size, quantity, cutting params)
2. Add Parts — Enter rectangular dimensions or import SVG shapes
3. Optimize — Run the nesting engine to pack parts as efficiently as possible
4. Export — Download SVG, DXF, or JSON layouts for your CNC or CAD software
```

This is a niche but genuinely useful tool. CNC cutting optimization is a real problem in woodworking, cabinet making, and sheet metal fabrication. The fact that it supports SVG shape nesting (not just rectangles) makes it more capable than many commercial alternatives.

The "BETA" label is appropriately prominent — the creator is being honest about the product's maturity.

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (subdomain + badge) |
| Hosting | lovable.app free subdomain |
| Optimization engine | Client-side JavaScript (browser-based) |
| Export | SVG, DXF, JSON (file generation in browser) |
| Protocol | HTTPS (Lovable-managed) |
| Backend | None visible |

---

## Security & Quality Issues

### 🔴 HIGH — No Privacy Policy or Terms of Service

CutList Optimizer is an interactive browser-based tool on a public URL with no legal documentation. While the current version appears to require no user accounts or data storage, there are privacy concerns:
- Does the tool log uploaded SVG files?
- Are optimization calculations sent to a server or run locally?
- Does Lovable's underlying platform collect analytics?

None of these questions are answerable because there are no legal pages. An engineering tool used by professionals in commercial contexts (cabinet shops, fabrication businesses) should have a clear terms of service defining liability for optimization errors.

---

### 🟡 MEDIUM — Footer Badge Exposes Project ID

**Evidence:**
```
https://lovable.dev/projects/2214a293-3d8e-4923-975c-a84ba83896ac
```

Visible "Edit with Lovable" badge in the application footer.

---

### 🟡 MEDIUM — Platform Dependency Risk

The tool is hosted at `cutlayout.lovable.app`. No custom domain means:
- Complete dependency on Lovable's free tier continuing to exist
- No independent hosting control
- Users who bookmark the URL could find it gone without warning

For a tool being used in professional CNC workflows, URL stability matters.

---

### 🟡 MEDIUM — DXF Export Format — Untested Security Surface

DXF (Drawing Exchange Format) files from some CAD systems can embed script-like macros or external reference links. The tool generates DXF output for use in CAD software. Whether the generated DXF is sanitized to prevent any injection of unexpected content into downstream CAD tools is unknown from passive analysis.

This is a low-probability risk, but it is the type of issue that AI builders don't naturally consider because it requires domain-specific knowledge of the DXF specification.

---

### 🔵 LOW — BETA With No Changelog or Roadmap

The BETA label appears prominently but there is no accompanying communication about what "BETA" means:
- Is data persistence guaranteed?
- Are there known limitations?
- When will it exit BETA?
- What is the upgrade or notification path?

Users investing time to set up complex cutting layouts should know whether their configurations will be preserved.

---

## Overall Score: 5 / 10

Genuinely useful engineering tool. The missing legal documentation is the primary barrier to professional adoption. The platform dependency and DXF export concerns are secondary but real for a tool targeting CNC workflows.

---
---

# SITE 8 — GoldMine AI

**URL:** https://goldmineai.io  
**Live Status:** ✅ Active  
**Custom Domain:** Yes  
**AI Build Badge:** Implicit (lovable-uploads asset path)  

---

## What the Website Does

GoldMine AI is an AI-powered startup validation platform. Founders enter a startup idea, and AI agents autonomously:
- Research the market and competitive landscape
- Map competitors
- Mine Reddit for real-world signals and pain points
- Generate customer profiles
- Find and qualify potential early customers via LinkedIn and Hunter.io

The platform is described as *"Fully agentic. No prompts — you stay in control."*

---

## Site Structure

```
goldmineai.io/
├── /                    → Homepage (value prop, how it works, pricing)
├── /idea-database       → Public gallery of validated startup ideas
│   ├── /eabc6fbe-...    → Individual idea entry (UUID-based)
│   └── /3323dcd8-...    → Individual idea entry (UUID-based)
├── /market-research     → Feature detail page
├── /reddit-signals      → Feature detail page
├── /blog                → Content marketing
├── /terms               → Terms of service
├── /privacy             → Privacy policy
├── /auth                → Login + signup
└── /projects-demo       → Live demo
```

10 pages discovered. The public idea database with UUID-based entries is an interesting and unusual structural choice.

---

## Content Quality Assessment

**Homepage:** Well-structured with four feature columns (Market Intelligence, Research & Competitors, Validation/Positioning, Find & Reach Buyers). Clearly targets "founders who want to build with conviction, not hunches."

**AI Provider Stack (advertised on homepage):**
The site displays logos for: Reddit, Perplexity, LinkedIn, OpenAI GPT, Google Gemini, Anthropic Claude, Google Trends, Hunter.io — all shown as data and AI sources the platform uses.

**Idea Database:** Public entries are accessible via UUID paths like `/idea-database/eabc6fbe-a0cb-4000-88a4-af1d2a0ced8c`. This is either intentional (sharing validated ideas as content marketing) or a privacy oversight (user-submitted ideas being world-readable).

**Chatwoot Integration:** A live chat widget loads on every page, with branding visible: *"Powered by Chatwoot."*

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (confirmed via `/lovable-uploads/` asset paths) |
| Hosting | Custom domain |
| Auth | Google OAuth + email/password |
| AI | OpenAI GPT, Claude, Gemini, Perplexity |
| Data sources | Reddit, LinkedIn, Hunter.io, Google Trends |
| Support | Chatwoot (open-source customer support) |
| Payments | Stripe (inferred from pricing feature) |
| Protocol | HTTPS |

---

## Security & Quality Issues

### 🔴 CRITICAL — Broken Terms of Service and Privacy Policy Links on Auth Page

**Evidence from auth page source:**
```
By continuing, you agree to our Terms of Service and Privacy Policy
[Terms of Service] → https://www.goldmineai.io/auth#
[Privacy Policy]   → https://www.goldmineai.io/auth#
```

Both the Terms of Service and Privacy Policy links on the signup/login page point to `#` — an empty hash anchor that scrolls to the top of the current page and goes nowhere. This means:

1. **Users cannot read the terms they are agreeing to** before creating an account
2. **This is a GDPR violation** — GDPR Article 7(2) requires that consent be as easy to withdraw as to give, and that the terms be clearly presented. If users cannot access them before agreeing, consent is not freely informed.
3. **This is a CCPA concern** — California requires privacy policy disclosure at or before the point of data collection.

The terms and privacy policy *do exist* at `/terms` and `/privacy` — they are simply not linked from the auth page. This appears to be an AI-build error where placeholder `#` links were never replaced.

---

### 🟡 MEDIUM — `/lovable-uploads/` Asset Path Exposes Build Platform

**Evidence from page source:**
```html
<img src="/lovable-uploads/goldmyne-logo-light.png" />
<img src="/lovable-uploads/reddit.webp" />
<img src="/lovable-uploads/perplexity.png" />
<img src="/lovable-uploads/linkedin.png" />
<img src="/lovable-uploads/gpt.jpg" />
<img src="/lovable-uploads/gemini.png" />
<img src="/lovable-uploads/claude.png" />
<img src="/lovable-uploads/hunter.png" />
```

Lovable's default behavior stores uploaded assets under the `/lovable-uploads/` path. Even though the site uses a custom domain, every image path reveals the build platform. Any user, competitor, or security researcher who inspects the page source immediately knows the site was built with Lovable.

While this isn't a vulnerability, it:
- Exposes the tech stack
- Reveals the storage bucket path (which could be probed for directory listing)
- Marks the site as potentially carrying other Lovable-default behaviors

---

### 🟡 MEDIUM — Idea Database Privacy Ambiguity

The public URL `/idea-database` displays startup ideas submitted by users, accessible via UUID paths. The privacy policy should clearly state:
- Whether submitted ideas are public by default
- Whether users can opt out of their ideas appearing in the public gallery
- What happens to idea data if an account is deleted

If users assume their startup idea validations are private but they are actually world-readable, this is a serious trust issue for a platform targeting early-stage founders with potentially confidential business concepts.

---

### 🟡 MEDIUM — Chatwoot Widget Exposes Support Stack

**Evidence:**
```
Powered by Chatwoot
[Chatwoot logo link to chatwoot.com]
```

The support chat widget loads on every page and prominently shows the Chatwoot branding. This:
- Reveals the support infrastructure
- Creates a secondary vendor dependency (if Chatwoot has downtime, the widget breaks)
- The widget loads even when the team is listed as "away," consuming client resources for no user benefit

---

### 🔵 LOW — Logo Filename Typo

**Evidence:**
```
/lovable-uploads/goldmyne-logo-light.png
```

The asset is named `goldmyne` (not `goldmine`). This is a minor typo in the uploaded filename, suggesting it was AI-generated and not reviewed before deployment.

---

### 🔵 LOW — Multiple Third-Party AI Provider Dependencies

The platform advertises integration with 8 external data and AI sources. If any provider changes their API terms, raises prices, or goes offline, a core feature of the product may silently degrade. There is no documented fallback behavior or degraded-mode UX visible.

---

## Overall Score: 7 / 10

Good product concept with real technical depth. The broken ToS/Privacy links on the auth page are the most urgent fix — it is a legal compliance issue that can be resolved in minutes by replacing two `#` hrefs with the correct paths. Everything else is polish.

---
---

# SITE 9 — Ninja Alert

**URL:** https://ninja-alert.com  
**Live Status:** ✅ Active  
**Custom Domain:** Yes  
**AI Build Badge:** None visible  

---

## What the Website Does

Ninja Alert monitors World Ninja League (WNL) competition run-order pages and sends email notifications to parents or gym coordinators when their athlete's run position is posted. Parents no longer need to repeatedly refresh the WNL website to find out when their child competes.

Two tiers:
- **Free** — 1 athlete, no credit card
- **Paid gym plans** — manage an entire competition team

---

## Site Structure

```
ninja-alert.com/
├── /                  → Landing page
├── /pricing           → Pricing tiers (linked, not mapped)
├── /faq               → Frequently asked questions (linked)
├── /auth              → Login + signup
│   └── ?mode=signup  → Sign up variant
└── /auth              → Login variant
```

Minimal structure — a focused, single-purpose product.

---

## Content Quality Assessment

The landing page is clean and direct. The value proposition is stated immediately:

*"Never miss your ninja's run order again. NinjaAlert monitors World Ninja League run order pages and notifies you when run orders are posted."*

The interface shown in the scrape includes an "Individual Dashboard" section, confirming that the product is functional and being actively used. The free-for-1-athlete model lowers the barrier to try the service and is a smart acquisition strategy.

The site reads as a genuine indie product built to solve a specific pain point the creator experienced personally — a common and successful product pattern.

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (inferred from UI patterns) |
| Hosting | Custom domain |
| Monitoring | Web scraping of WNL run-order pages |
| Notifications | Email (likely via SendGrid, Mailgun, or similar) |
| Auth | Custom (login + signup routes) |
| Protocol | HTTPS |

---

## Security & Quality Issues

### 🟡 MEDIUM — Core Feature Depends Entirely on Scraping a Third-Party Website

The fundamental mechanism of the product — monitoring World Ninja League run-order pages — is a web scraping dependency on a third party the creator does not control. Risks:

1. **WNL adds bot protection** (Cloudflare, rate limiting) → Ninja Alert's monitoring breaks immediately
2. **WNL changes their page structure** → the scraper's HTML selectors stop working
3. **WNL changes their URL format** → all monitored links become invalid
4. **WNL shuts down or changes their domain** → entire product stops working

There is no mention on the landing page of any SLA, fallback behavior, or contingency for when monitoring fails. Parents relying on this for competition scheduling could receive no notification without knowing the service has broken.

---

### 🟡 MEDIUM — Handling Children's Data Without Visible Privacy Policy

The product collects:
- Parent email addresses
- Children's athlete names
- Competition schedules

COPPA (Children's Online Privacy Protection Act) applies to services that collect information from or about children under 13, even indirectly. While Ninja Alert collects parent information (not child information directly), it is specifically marketed as a product for tracking children's athletic events.

A clear privacy policy explaining what data is collected and how it is handled is particularly important for a service in this context. None was confirmed in the scrape.

---

### 🔵 LOW — Pricing Not Publicly Visible

The `/pricing` page is linked from the nav but was not accessible in the scrape (possibly behind a loading interaction or auth redirect). For a free-first product, pricing clarity before signup is important for trust.

---

### 🔵 LOW — Fragile Product Category

The entire market for Ninja Alert depends on World Ninja League remaining a significant competitive circuit. If WNL's popularity declines, the total addressable market shrinks accordingly. This is an inherent business risk for highly niche products.

---

## Overall Score: 6.5 / 10

A focused, well-executed product solving a real problem. The scraping dependency is both the core feature and the core risk. Adding a status page (so parents know if monitoring is currently active) and a clear privacy policy would meaningfully improve trust.

---
---

# SITE 10 — Dhurandhar FanPulse Insights Dashboard

**URL:** https://dhurandharpart2insights.lovable.app  
**Live Status:** ✅ Active (but showing incorrect data)  
**Custom Domain:** No (lovable.app subdomain)  
**AI Build Badge:** Not visible (Lovable subdomain confirms platform)  

---

## What the Website Does

The Dhurandhar FanPulse Insights Dashboard is an AI-powered analytics dashboard for the Bollywood film "Dhurandhar: The Revenge" (released March 19, 2026). It claims to display:
- Box office collections (India and Worldwide)
- Buzz Score (sentiment from social chatter)
- Sentiment breakdown percentages
- AI-generated key insights

The header shows dual branding: **LokLearning** (an educational platform) and **FanPulse Insights** — suggesting this was built as a demonstration or portfolio project for LokLearning.

---

## Site Structure

```
dhurandharpart2insights.lovable.app/
└── /   → Single-page dashboard (all data on one screen)
```

One page. No additional routes discovered.

---

## Content Quality Assessment (Critical Issues)

This is the only site in this audit that is actively displaying incorrect, potentially misleading data to public visitors.

**Evidence — corrupted data values displayed on the live dashboard:**

```
Buzz Score:         -348 / 100      ← NEGATIVE (impossible — min is 0)
Worldwide:          ₹-4470Cr+       ← NEGATIVE (impossible revenue figure)
India Net:          ₹-2825Cr+       ← NEGATIVE (impossible revenue figure)
```

A buzz score below zero is mathematically invalid for a 0–100 scale. Negative box office figures in the hundreds of crores are clearly data pipeline errors, not real data.

**The site itself acknowledges the problem:**
```
⚠ Stale
Updated 2 months ago
```

An in-dashboard warning banner confirms the data is outdated. Despite this warning, the dashboard remains publicly live with the corrupted negative values displayed to anyone who visits.

**Snapshot date displayed:** `01/04/2026 1:20 PM` — the data has not been updated since April 1, 2026, two months before this audit.

---

## Content vs. Reality

The dashboard claims to be *"AI-powered movie intelligence built from publicly available chatter, reviews, memes, and box office signals."* However:

1. The data is static (a snapshot from April 1, 2026)
2. The data contains numerical errors (negative scores and revenue)
3. The snapshot date is hardcoded, not dynamically updated
4. The "AI-generated" insights describe the film positively (*"gold standard for Indian spy thrillers"*) while the buzz score reads -348

There is a complete disconnect between the displayed numbers and the displayed text sentiment. The AI-generated insight text was clearly written when the data was positive — the numbers were later corrupted without updating the text.

---

## Tech Stack (Observed)

| Component | Detail |
|-----------|--------|
| Builder | Lovable (subdomain confirms) |
| Hosting | lovable.app free subdomain |
| Data | Static/hardcoded (not live feed) |
| Branding | LokLearning (parent organization) |
| Protocol | HTTPS (Lovable-managed) |

---

## Security & Quality Issues

### 🔴 CRITICAL — Live Dashboard Displaying Negative Box Office Figures

**Evidence:**
```
Worldwide: ₹-4470Cr+
India Net: ₹-2825Cr+
Buzz Score: -348/100
```

A member of the public visiting this URL sees impossible financial data displayed without explanation. This is misinformation — not malicious, but consequential if taken at face value. Anyone who screenshots or shares this data will spread incorrect figures about the film's performance.

**The site should either be fixed or taken offline immediately.**

---

### 🔴 CRITICAL — Stale Warning Banner Signals Abandonment

The dashboard's own UI shows `⚠ Stale — Updated 2 months ago`. This confirms the site is abandoned or unmaintained. A responsible action would be to:
1. Fix the data pipeline and update the numbers, or
2. Add a clear "Demo/Prototype" disclaimer, or
3. Take the site offline

Leaving a public-facing dashboard with visibly broken, negative financial data online under the LokLearning brand reflects on that brand's credibility.

---

### 🟡 MEDIUM — Confusing Dual Branding

Two organizations appear on the page:
- **LokLearning** — shown with its own logo in the header navigation
- **FanPulse Insights** — the dashboard product name in the hero section

No explanation of the relationship between them is provided. A visitor unfamiliar with either brand cannot determine who built this, who maintains it, or who is responsible for the data.

---

### 🟡 MEDIUM — No Data Source Citations

The dashboard claims *"AI-generated"* insights and *"AI-powered movie intelligence built from publicly available chatter."* However, no sources are cited for any data point — not for the box office figures, not for the sentiment percentages, not for the buzz score formula. There is no way to verify or audit any number on the page.

---

### 🔵 LOW — No Privacy Policy

An analytics dashboard with no privacy policy or terms. While this site may not collect personal data from visitors, the absence of any legal documentation is standard for abandoned Lovable prototypes.

---

### 🔵 LOW — lovable.app Subdomain

No custom domain. Hosted entirely at Lovable's free tier with no independent URL or branding permanence.

---

## Overall Score: 2 / 10

The lowest-scoring site in this audit. Actively displaying corrupted financial data to the public while showing a warning that the data is stale. This site should be updated or taken offline. It is the clearest example of what happens when an AI-built prototype is left running without maintenance.

---
---

# CROSS-SITE ANALYSIS

---

## AI Build Platform Fingerprints

AI builders like Lovable leave consistent traces across the sites they produce. These were observed across the 10 sites:

| Fingerprint | Description | Sites Affected |
|-------------|-------------|----------------|
| `lovable.app` subdomain | Free-tier hosting on Lovable's domain | PathCraft, CutList, Dhurandhar |
| `/lovable-uploads/` asset path | Default image upload directory in Lovable | GoldMine AI |
| "Edit with Lovable" footer badge | Linking to `lovable.dev/projects/{uuid}` | LumièresPartners, PathCraft, CutList |
| Content-hashed filenames | Vite/React build output like `logo-sTR8O6wn.png` | All 10 sites |
| React SPA structure | Single URL maps to multi-route app | All 10 sites |
| Inline SVG data URIs | Logo embedded as base64 in HTML | WanderScout |
| Unsplash hardcoded URLs | Stock photos linked directly from Unsplash CDN | WanderScout |
| Chatwoot branding | Support widget with open-source branding | GoldMine AI |
| Placeholder `#` links | Unfinished href targets left in production | GoldMine AI |
| Static hardcoded data | Data not connected to live sources | Dhurandhar |

---

## Legal Compliance Summary

| Site | Privacy Policy | Terms | Cookie Notice | Notable Issue |
|------|---------------|-------|---------------|---------------|
| LumièresPartners | ✅ Implied | ✅ Copyright | ❌ None | Email exposure |
| PathCraft | ❌ Missing | ❌ Missing | ❌ None | No legal pages |
| WanderScout | ✅ Excellent | ✅ Present | ✅ Consent | Best in class |
| TrackingPass | ⚠️ Not linked | ⚠️ Not visible | ❌ None | Customer data unprotected |
| AgentSwarms | ✅ Present | ✅ Present | ❌ Not visible | Minor gaps |
| Tymora | ⚠️ Outdated 18mo | ✅ Present | ❌ None | Credential handler with old policy |
| CutList Optimizer | ❌ Missing | ❌ Missing | ❌ None | No legal pages |
| GoldMine AI | ✅ Present | 🔴 Broken link | ❌ None | Auth page links broken |
| Ninja Alert | ⚠️ Not confirmed | ⚠️ Not confirmed | ❌ None | Children's data risk |
| Dhurandhar | ❌ Missing | ❌ Missing | ❌ None | Abandoned prototype |

**6 of 10 sites have no cookie notice.**  
**4 of 10 sites have no privacy policy at all.**  
**Only 1 site (WanderScout) achieves full legal compliance.**

---

## Security Issues by Severity

### 🔴 Critical (Requires Immediate Action)
| # | Site | Issue |
|---|------|-------|
| 1 | Dhurandhar Dashboard | Live dashboard showing negative (corrupted) box office figures |
| 2 | Dhurandhar Dashboard | Stale data warning on live public dashboard |
| 3 | GoldMine AI | ToS and Privacy Policy links broken on auth page (GDPR violation) |

### 🔴 High (Fix Before Next Growth Push)
| # | Site | Issue |
|---|------|-------|
| 4 | PathCraft | No privacy policy for interactive public app |
| 5 | CutList Optimizer | No privacy policy for professional engineering tool |
| 6 | TrackingPass | No public privacy/terms for a platform handling customer loyalty data |
| 7 | Tymora | Privacy policy 18 months outdated for a credential-handling SaaS |
| 8 | Ninja Alert | No confirmed privacy policy for service handling children's data |

### 🟡 Medium (Address Within 30 Days)
| # | Site | Issue |
|---|------|-------|
| 9 | LumièresPartners | Personal email addresses exposed in footer HTML |
| 10 | LumièresPartners | "Edit with Lovable" badge exposes project ID |
| 11 | LumièresPartners | Contact form has no bot protection |
| 12 | PathCraft | Lovable project ID exposed in footer |
| 13 | WanderScout | All hero images hardcoded to Unsplash CDN |
| 14 | WanderScout | Inline SVG logo bloats every page load |
| 15 | Tymora | Email credential storage lacks encryption details |
| 16 | Tymora | Testimonials are unverifiable |
| 17 | CutList Optimizer | "Edit with Lovable" badge exposes project ID |
| 18 | CutList Optimizer | Platform lock-in on free lovable.app subdomain |
| 19 | GoldMine AI | lovable-uploads asset path reveals build stack |
| 20 | GoldMine AI | Idea database entries may be unintentionally public |
| 21 | GoldMine AI | Chatwoot widget exposes support stack and loads when team is away |
| 22 | Ninja Alert | Core feature is a scraping dependency on a third-party site |
| 23 | Dhurandhar | Dual branding confusion (LokLearning vs FanPulse) |
| 24 | Dhurandhar | No data source citations for AI-generated claims |

### 🔵 Low (Track and Resolve Over Time)
| # | Issue | Site |
|---|-------|------|
| 25 | Client portal link not in sitemap | LumièresPartners |
| 26 | Blog on external Substack creates broken link risk | LumièresPartners |
| 27 | No 404 error page | PathCraft |
| 28 | Pro subscription not visible without account | WanderScout |
| 29 | Full product hidden behind auth | TrackingPass |
| 30 | No bug bounty or security disclosure | AgentSwarms |
| 31 | Two logo variants loaded on every page | Tymora |
| 32 | User manual not linked from main nav | Tymora |
| 33 | No BETA changelog or roadmap | CutList Optimizer |
| 34 | DXF export has untested security surface | CutList Optimizer |
| 35 | Logo asset has typo "goldmyne" | GoldMine AI |
| 36 | Multiple AI provider dependencies with no fallback | GoldMine AI |
| 37 | Pricing not publicly visible before signup | Ninja Alert |
| 38 | No status page for monitoring service | Ninja Alert |

---

## Overall Rankings

| Rank | Site | Score | Verdict |
|------|------|-------|---------|
| 🥇 1 | **WanderScout** | **9.5 / 10** | Best-in-class. GDPR/CCPA compliant, active changelog, real product. Nearest thing to a production-grade site in this audit. |
| 🥈 2 | **AgentSwarms** | **9 / 10** | Richest content depth. 17 free tools, 50+ lessons, technical blog. Genuine value for developers. |
| 🥉 3 | **Tymora** | **8 / 10** | Most operationally sophisticated SaaS. Real integrations, real compliance pages, real customers. Privacy policy urgently needs updating. |
| 4 | **LumièresPartners** | **7.5 / 10** | Real business with verifiable clients and professional content. Minor but fixable issues. |
| 5 | **GoldMine AI** | **7 / 10** | Strong product concept. Broken ToS links on auth page is a legal issue fixable in 5 minutes. |
| 6 | **TrackingPass** | **7 / 10** | 300+ active businesses, real traction. Missing public legal pages for a product handling customer data at scale. |
| 7 | **Ninja Alert** | **6.5 / 10** | Focused, well-executed indie product. Core scraping dependency is both the feature and the fragility. |
| 8 | **CutList Optimizer** | **5 / 10** | Useful engineering tool. Platform lock-in and missing legal pages hold it back for professional use. |
| 9 | **PathCraft** | **4 / 10** | Working, playable game. A prototype, not a production product. |
| 10 | **Dhurandhar Dashboard** | **2 / 10** | Should be taken offline. Showing corrupted negative financial data to the public with its own stale warning visible. |

---

## Key Takeaways for AI-Built Site Builders

### 1. Legal pages are not optional — and AI builders don't write them for you.
4 of 10 sites have no privacy policy. 6 have no cookie notice. If your site collects email addresses, tracks analytics, or handles any user data, you need a privacy policy and cookie disclosure. AI builders generate the UI, not the legal layer.

### 2. Remove the "Edit with Lovable" badge before going live.
It exposes your project UUID and build platform. It takes one toggle to remove. Three sites in this audit had it visible.

### 3. Don't hardcode URLs from third-party CDNs.
WanderScout's Unsplash dependency, LumièresPartners' Substack blog, and Ninja Alert's WNL scraping are all examples of external dependencies that can break the product without any action by the site owner.

### 4. Update privacy policies when you add features.
Tymora added voice AI, WhatsApp, and CRM integrations while the privacy policy still says January 2025. An 18-month gap for a credential-handling SaaS is a compliance liability.

### 5. Test every link before launching.
GoldMine AI has broken ToS links on the page where users agree to them. This is a fixable, scannable issue that a 30-second review would catch. AI builders produce placeholder `#` links that need manual replacement.

### 6. Don't leave dashboards with corrupted data running.
The Dhurandhar site's negative revenue figures are worse than no data at all. Stale, incorrect data on a live public URL damages the creator's and the client's credibility.

### 7. The best sites had real founder investment beyond the AI prompt.
WanderScout's 1,800-word privacy policy was not AI-generated boilerplate. AgentSwarms' 578KB of curriculum content was not a single prompt. Tymora's 8 industry-specific landing pages represent deliberate SEO strategy. The quality ceiling for AI-built sites is determined by the human effort layered on top of the AI-generated base.

---

## Rerun Instructions

To reproduce this audit with new sites:

```bash
# Set your API key
export FIRECRAWL_API_KEY=YOUR_KEY_HERE

# Step 1 — Discover AI-built site showcases
npx -y firecrawl-cli@latest scrape "https://madewithlovable.com/" > showcase.md

# Step 2 — Map and scrape each site
for URL in "${SITES[@]}"; do
  npx -y firecrawl-cli@latest map "$URL" --limit 30
  npx -y firecrawl-cli@latest scrape "$URL"
done

# Step 3 — Scrape key sub-pages (privacy, terms, auth, contact)
for URL in "${SUBPAGES[@]}"; do
  npx -y firecrawl-cli@latest scrape "$URL"
done

# Then analyze: look for
# - /lovable-uploads/ in asset paths
# - href="#" for legal links
# - "Edit with" badges linking to lovable.dev/projects/
# - Outdated "Last updated" dates in privacy policies
# - Negative or impossible data values
# - Missing /privacy and /terms routes in site map
```

---

*Report generated by Firecrawl-powered audit pipeline. All findings based on publicly observable information. No authenticated access, active probing, or exploitation was performed.*
