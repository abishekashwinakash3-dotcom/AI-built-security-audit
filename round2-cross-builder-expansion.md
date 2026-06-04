# Round 2 — Cross-Builder Expansion: 30 AI-Built Sites, 4 Builders

**Date:** June 5, 2026
**Auditor:** Automated Firecrawl pipeline + analysis
**Builders covered:** Lovable, Bolt, Replit, v0 (Vercel)
**Method:** URL discovery, liveness verification, full-page scraping, SPA-aware legal-page probing, passive content analysis
**Companion documents:** [`ai-built-websites-report.md`](ai-built-websites-report.md) (the original 10-site Lovable audit) and [`README.md`](README.md)

> This is a **standalone follow-up** to the first audit. The original report — 10 Lovable sites, deep per-site write-ups — is unchanged and lives in [`ai-built-websites-report.md`](ai-built-websites-report.md). This document adds **20 new sites across four builders** and the cross-platform analysis. All findings remain **passive only**: no authenticated access, active probing, or exploitation.

---

## Why I Expanded the Study

The first audit looked at 10 sites, all built with Lovable, and found a consistent set of legal, privacy, and platform-hygiene gaps. That raised an obvious objection: *maybe these gaps are just a Lovable thing.*

So I asked a bigger question — is this a **Lovable** problem, or an **AI-built-website** problem? — and sourced 20 more live sites, this time from **Bolt**, **Replit**, and **v0** as well, running the identical passive audit.

The answer: it generalizes. The builder changes; the missing basics don't.

---

## A Finding Before the Audit Even Started: You Can't Find v0 Sites

The intent was an even split across four builders. It wasn't achievable — and *why* is itself a security-relevant finding:

| Builder | Discoverability of live sites |
|---------|-------------------------------|
| **Bolt** | **High** — public gallery (madewithbolt.com) + predictable `*.bolt.host` / Netlify subdomains |
| **Lovable** | **High** — `*.lovable.app` subdomains are indexed, and HTML carries platform fingerprints |
| **Replit** | **Medium** — `*.replit.app` exists, but most apps sit behind an auth wall |
| **v0 (Vercel)** | **None** — deploys to generic `*.vercel.app` with no platform fingerprint in the HTML |

There is no passive way to tell a v0-built site from any other Vercel site. Final sample: **Bolt ×10, Lovable ×8, Replit ×2, v0 ×0.**

The asymmetry cuts both ways. v0's un-fingerprintability is a **defensive strength** — a site nobody can identify as built by a given tool can't be swept up by an attacker scanning for that tool's known weaknesses. Lovable sits at the opposite extreme: **8 of 8** audited sites render their internal project UUID into the page source, effectively advertising their build platform to anyone who views source.

---

## Methodology Delta

Same Firecrawl pipeline as Round 1 (map → scrape → sub-page probe), with two additions for the larger, cross-builder sample:

1. **Liveness verification** — every candidate was HTTP-checked (follow redirects, confirm `200`) before auditing; dead candidates (e.g. `eden.pm`, `zombiepalooza.app`) were dropped.
2. **SPA-aware legal-page detection** — many AI-built sites are single-page apps whose server returns the same `index.html` for *every* path, so a `200` on `/privacy` does **not** prove a policy exists. Each `/privacy` route was fetched and its **rendered content** checked for actual policy language ("we collect", "personal data", "GDPR", "cookies"). This distinguishes three states:
   - **linked** — policy is reachable from the site,
   - **unlinked** — policy exists at `/privacy` but is not linked anywhere,
   - **none** — no policy content renders at all.

Analysis is reproducible from the scripts in this repo: `.firecrawl/analyze.js` (signal extraction) and `.firecrawl/score.js` (scoring).

---

## Scored Results (20 New Sites)

| # | Site | Builder | Type | Score |
|---|------|---------|------|-------|
| 1 | Prilo | Bolt | AI study platform (FBLA students) | 2.5 / 10 |
| 2 | GrepJob | Bolt | Developer job board | 3 / 10 |
| 3 | Clean-URL | Bolt | URL cleaner utility | 4.5 / 10 |
| 4 | MyNextChore | Bolt | Chore-picking mini game | 4.5 / 10 |
| 5 | Dachly | Bolt | Habit / accountability app | 5.5 / 10 |
| 6 | Tetdle | Bolt | Word-building puzzle game | 6 / 10 |
| 7 | Couples Hub | Bolt | Relationship activity app | 7 / 10 |
| 8 | Openmark | Bolt | Bookmark manager | 7.5 / 10 |
| 9 | ToolsApp | Bolt | Online calculators/converters | 7.5 / 10 |
| 10 | Siam Ferry | Bolt | Thai island ferry booking | 10 / 10 |
| 11 | AI Dash Boss | Lovable | AI dashboard tool | 4.5 / 10 |
| 12 | Notaloy (নোটালয়) | Lovable | Bangladesh academic notes platform | 4.5 / 10 |
| 13 | StudySoul | Lovable | AI-powered learning platform | 4.5 / 10 |
| 14 | AI Fit Coach Hub | Lovable | AI fitness coach | 5.5 / 10 |
| 15 | Meal Share Plan | Lovable | Meal planning app | 5.5 / 10 |
| 16 | Founder Friendly Builds | Lovable | Builder landing page | 8 / 10 |
| 17 | SheBuilds | Lovable | Women-in-tech community | 9.5 / 10 |
| 18 | StudyCrew | Lovable | AI study tools for students | 9.5 / 10 |
| 19 | Replit Community Hub | Replit | Community platform | 3.5 / 10 |
| 20 | Replit Turns 10 | Replit | Anniversary microsite | 10 / 10 |

### Scoring Rubric

Each site starts at 10 and loses points per observed issue:

| Deduction | Points |
|-----------|--------|
| No privacy policy anywhere | −3 |
| Privacy policy exists but unlinked | −1.5 |
| No terms of service linked | −1 |
| Analytics running with no cookie consent | −1.5 |
| Personal email in raw HTML | −1.5 |
| Support/role email in raw HTML | −1 |
| Build-platform project UUID exposed | −0.5 |
| Placeholder/default page title in production | −1 |
| Hotlinked external images | −0.5 |
| Student/youth audience + no privacy policy | −1 |
| Homepage is a non-functional redirect shell | −1.5 |

---

## The Central Result: Same Failures, Different Signatures

Each builder has a **signature failure mode** baked into its defaults — but the *legal/privacy basics* are missing regardless of builder.

| Builder | Sites | Avg score | No policy | Analytics w/o consent | Build fingerprint leak |
|---------|-------|-----------|-----------|-----------------------|------------------------|
| Bolt | 10 | 5.8 | 4 / 10 | **8 / 10** | 2 / 10 |
| Lovable | 8 | 6.4 | 5 / 8 | 1 / 8 | **8 / 8** |
| Replit | 2 | 6.8 | 1 / 2 | 1 / 2 | 2 / 2 |
| v0 | 0 | — | — | — | — |

- **Bolt's signature:** analytics (Google Analytics, PostHog, Microsoft Clarity) wired in with **no cookie consent** — 8 of 10 sites.
- **Lovable's signature:** the internal project UUID is rendered into the page HTML on **8 of 8** sites (e.g. `99eaf9a4-f914-495c-8d11-8c95e174dd4e`), a direct evolution of the Round 1 "Edit with Lovable" badge finding.

**The gaps that held across every builder:**

| Issue | New 20 |
|-------|--------|
| No cookie consent notice | **19 / 20 (95%)** |
| No privacy policy linked on the site | **12 / 20 (60%)** |
| No terms of service linked | **12 / 20 (60%)** |

The builder changes; the missing legal and privacy basics don't.

---

## Notable Findings

### 🔴 CRITICAL — Prilo: session-replay analytics on a teenage audience, no privacy policy

Prilo is an *"AI Study Platform for FBLA"* (Future Business Leaders of America — a middle/high-school organization). It runs **Google Analytics and Microsoft Clarity** (Clarity captures session replays: cursor movement, clicks, scroll) with **no privacy policy** anywhere, and exposes three personal emails in HTML (`rishi@prilo.ai`, `timmy@prilo.ai`, `support@prilo.ai`). Session-recording a predominantly minor audience without a privacy notice is squarely in COPPA/FERPA territory. This is the cross-builder echo of Round 1's Ninja Alert — with replay analytics on top.

### 🔴 HIGH — GrepJob: no policy, behavioral analytics, founder email exposed

Developer job board running **PostHog** (product analytics) with no privacy policy or terms, founder email `kyle@grepjob.com` in the HTML, and a project UUID exposed. `/privacy` returns a genuine 404 — the pages simply don't exist.

### 🟡 MEDIUM — Couples Hub: a personal Gmail shipped to production

`mdmeetstechie@gmail.com` is rendered directly in the page source — a personal Gmail, not a role alias, exposed to spam harvesters.

### 🟡 MEDIUM — The "unlinked policy" trap (Dachly, Tetdle)

Both **wrote** a real privacy policy living at `/privacy`, but **neither links to it** from anywhere on the site. A policy users can't find provides the same protection as no policy at all. Detectable only by probing routes directly — exactly the SPA-aware check added this round.

### 🔵 LOW — Placeholder titles left in production (AI Dash Boss, Replit Community Hub)

AI Dash Boss's browser tab still reads the Lovable default **"Lovable App"**; it was never set. A minor issue, but a reliable signal that the post-generation review never happened.

---

## Combined Totals — All 30 Sites

Combining the original 10 (see [`ai-built-websites-report.md`](ai-built-websites-report.md)) with these 20:

| Issue | Round 1 (10) | Round 2 (20) | All 30 |
|-------|-------------|-------------|--------|
| No privacy policy at all | 4 (40%) | 10 (50%) | **14 (47%)** |
| No cookie consent notice | 6 (60%) | 19 (95%) | **25 (83%)** |
| Build-platform fingerprint/ID exposed | 3 (30%) | 10 (50%) | **13 (43%)** |
| Personal/founder email in raw HTML | 1 (10%) | 5 (25%) | **6 (20%)** |
| Average score | 6.6 / 10 | 6.2 / 10 | **~6.3 / 10** |

Across 30 sites and four AI builders, **nearly half ship with no privacy policy** and **more than four in five run with no cookie consent.** The conclusion from Round 1 generalizes: these are not Lovable problems — they are the default state of any AI-built site whose creator stopped at "it works."

---

## Per-Site Detail

### Prilo (Bolt) — 2.5/10
- Title: "Prilo - AI Study Platform for FBLA"
- Privacy: none · Terms: none · Cookie consent: no
- Analytics: Google Analytics, Microsoft Clarity (session replay)
- Exposed emails: rishi@prilo.ai, timmy@prilo.ai, support@prilo.ai

### GrepJob (Bolt) — 3/10
- Title: "GrepJob - Find Your Next Developer Role"
- Privacy: none (genuine 404) · Terms: none · Cookie consent: no
- Analytics: PostHog
- Exposed email: kyle@grepjob.com · Exposed UUID: c08d6b19-…-42052f46c6cb

### Clean-URL (Bolt) — 4.5/10
- Title: "Redirecting…" — homepage is a JS redirect shell with no rendered content
- Privacy: none · Terms: none · Cookie consent: no

### MyNextChore (Bolt) — 4.5/10
- Title: "mynextchore" — minimal single-page app
- Privacy: none (genuine 404) · Terms: none · Cookie consent: no
- Analytics: Google Analytics

### Dachly (Bolt) — 5.5/10
- Title: "Dachly"
- Privacy: **unlinked** (real policy at /privacy, not linked anywhere) · Terms: none · Cookie consent: no
- Analytics: Google Analytics · Exposed UUID: af53fd8e-…-1596b3f38d2e

### Tetdle (Bolt) — 6/10
- Title: "Tetdle - Word Building Puzzle Game"
- Privacy: **unlinked** · Terms: none · Cookie consent: no
- Analytics: Google Analytics

### Couples Hub (Bolt) — 7/10
- Title: "Couples Hub - Strengthen Your Bond, One Activity at a Time"
- Privacy: linked · Terms: linked · Cookie consent: no
- Analytics: Google Analytics · Exposed personal email: mdmeetstechie@gmail.com

### Openmark (Bolt) — 7.5/10
- Title: "Openmark"
- Privacy: linked · Terms: linked · Cookie consent: no
- Analytics: Google Analytics · Exposed email: support@openmark.co

### ToolsApp (Bolt) — 7.5/10
- Title: "Free Online Tools - Calculators, Converters & Utilities"
- Privacy: linked · Terms: linked · Cookie consent: no
- Analytics: Google Analytics · Exposed email: support@toolsapp.work

### Siam Ferry (Bolt) — 10/10
- Title: "Siam Ferry - Your Chill Island Ride"
- Privacy: linked · Terms: linked · No exposed identifiers. Cleanest Round 2 site.

### AI Dash Boss (Lovable) — 4.5/10
- Title: **"Lovable App"** (placeholder default, never changed)
- Privacy: none · Terms: none · Cookie consent: no
- Exposed UUID: 99eaf9a4-f914-495c-8d11-8c95e174dd4e

### Notaloy / নোটালয় (Lovable) — 4.5/10
- Title: "নোটালয় — Bangladesh's Academic Notes Platform"
- Privacy: none · Terms: none · Cookie consent: no · Student audience
- Exposed UUID: f3c4d447-3fec-486c-9c9d-8370f1f5fb88

### StudySoul (Lovable) — 4.5/10
- Title: "StudySoul - Transform Your Study Experience | AI-Powered Learning Platform"
- Privacy: none · Terms: none · Cookie consent: no · Student audience
- Exposed UUID: ab1d6141-faf1-4a2e-8667-3184f6497d20

### AI Fit Coach Hub (Lovable) — 5.5/10
- Title: "AI Fitness Coach - Personal Workout App for Home & Gym"
- Privacy: none · Terms: none · Cookie consent: no
- Exposed UUID: 7cec7427-ed10-46c2-855e-48a9bf1bd7a7

### Meal Share Plan (Lovable) — 5.5/10
- Title: "meal-share-plan" (slug used as title)
- Privacy: none · Terms: none · Cookie consent: no
- Exposed UUID: c2cddb6f-dc5d-400e-9bb7-9897065e2af6

### Founder Friendly Builds (Lovable) — 8/10
- Title: "Lovable AI - Build Apps 20x Faster with AI"
- Privacy: linked · Terms: linked · Cookie consent: no
- Analytics: Google Analytics · Exposed UUID: fb8ab0ac-1768-41c4-89b0-fd38b59e3103

### SheBuilds (Lovable) — 9.5/10
- Title: "SheBuilds on Lovable"
- Privacy: linked · Terms: linked · **Cookie consent: yes** (one of the few)
- Exposed UUID: 82dccdbf-1fd5-4d84-a205-3363ad908c5b

### StudyCrew (Lovable) — 9.5/10
- Title: "AI Study Tools for Students | StudyCrew"
- Privacy: linked · Terms: linked · Cookie consent: no
- Exposed UUID: 7153e527-9f90-4748-8313-dc98f6cccb99

### Replit Community Hub (Replit) — 3.5/10
- Title: "Replit Community | Connect. Build. Ship."
- Privacy: none · Terms: none · Cookie consent: no
- Analytics: Mixpanel

### Replit Turns 10 (Replit) — 10/10
- Title: "Replit Turns 10"
- Privacy: linked · Terms: linked · No exposed identifiers.

---

## Reproducing This Round

```bash
export FIRECRAWL_API_KEY=YOUR_KEY_HERE

# 1. Discover across builders
#    Lovable: firecrawl search "inurl:lovable.app <category>"
#    Bolt:    firecrawl scrape "https://madewithbolt.com/" -f links
#    Replit:  firecrawl search "inurl:replit.app <category>"
#    v0:      no reliable passive fingerprint (see Discoverability Finding)

# 2. Verify liveness, then scrape homepage HTML + links
for URL in "${SITES[@]}"; do
  curl -s -o /dev/null -w "%{http_code}" -L "$URL"          # drop non-200s
  firecrawl scrape "$URL" -f rawHtml,links,markdown --json -o "audit/$NAME.json"
done

# 3. SPA-aware legal check (200 != page exists on single-page apps)
firecrawl scrape "$URL/privacy" -f markdown --json           # test rendered content

# 4. Analyze + score  →  .firecrawl/analyze.js  and  .firecrawl/score.js
```

---

*Round 2 conducted June 2026 across 20 sites and four AI builders, as a follow-up to the original 10-site audit in [`ai-built-websites-report.md`](ai-built-websites-report.md). All findings are based on publicly observable information. No authenticated access, active probing, or exploitation was performed.*
