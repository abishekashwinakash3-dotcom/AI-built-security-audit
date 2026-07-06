# Project Brief — *If AI Built It, I Audit Its Security*

**Author:** Abishek Ashwin Akash
**Type:** Independent security research · Portfolio project
**Timeline:** June 2026 (two rounds)
**Scope:** 30 live, AI-built websites · 4 builders · 100% passive (no hacking)

---

## The One-Line Version

AI builders let anyone ship a full web app from a sentence. I audited 30 of those
live apps and found that nearly half ship with **no privacy policy** and more than
four in five run with **no cookie consent** — the builder changes, the missing
basics don't.

---

## Why I Did This

Tools like Lovable, Bolt, Replit, and v0 turn a single prompt into a deployed,
full-stack website. Impressive — but they generate the *UI*, not the
responsibility around it. I wanted to answer one question, then a bigger one:

1. **Round 1:** What do AI-built sites miss? (10 Lovable sites)
2. **Round 2:** Is this a *Lovable* problem, or an *AI-built-website* problem?
   (20 more sites across Bolt, Replit, and v0)

Answer: **it generalizes.** The same legal, privacy, and hygiene gaps show up no
matter which tool built the site.

---

## How I Did It (End to End)

Fully passive — I only read what every site already shows to any visitor. No
logins, no active probing, no exploitation.

```
For each site:
  1. DISCOVER  — find live AI-built sites (galleries, platform URL patterns)
  2. VERIFY    — HTTP-check every candidate is actually live (200) before auditing
  3. MAP       — Firecrawl discovers every public URL
  4. SCRAPE    — full page content pulled as markdown + raw HTML + links
  5. PROBE     — SPA-aware check of /privacy, /terms, /auth, /contact
  6. ANALYZE   — extract signals (missing legal pages, exposed emails,
                 build fingerprints, stale/broken data)
  7. SCORE     — rubric: each site starts at 10, loses points per issue
```

**A methodology insight I had to solve:** many AI sites are single-page apps that
return the *same* `index.html` for every path — so a `200` on `/privacy` does
**not** mean a policy exists. I added a rendered-content check to tell apart
*linked* / *unlinked* / *no policy* — this caught real policies that existed but
were never linked anywhere on the site.

**Stack:** Firecrawl API · Node.js + Python · reproducible analyze/score scripts.

---

## What I Found — All 30 Sites

| Issue | Round 1 (10) | Round 2 (20) | **All 30** |
|-------|-------------|-------------|-----------|
| No privacy policy at all | 40% | 50% | **47%** |
| No cookie consent notice | 60% | 95% | **83%** |
| Build-platform fingerprint/ID exposed | 30% | 50% | **43%** |
| Personal/founder email in raw HTML | 10% | 25% | **20%** |
| Average security score | 6.6 / 10 | 6.2 / 10 | **~6.3 / 10** |

**Each builder has a signature failure mode:**
- **Bolt** → analytics (GA / PostHog / Clarity) wired in with no cookie consent — 8 of 10.
- **Lovable** → internal project UUID rendered into page HTML — 8 of 8.
- **v0** → *can't even be found* — no passive fingerprint. That un-detectability is
  actually a defensive strength; Lovable's is the opposite.

---

## The Findings That Stuck With Me

- **Prilo (study platform for high-schoolers)** — runs Google Analytics + Microsoft
  Clarity *session replay* on a minor audience with **no privacy policy**. COPPA/FERPA territory.
- **Ninja Alert** — tracks children's competition schedules, emails parents, collects
  kids' names — no privacy policy anywhere.
- **GoldMine AI** — signup page asks you to agree to Terms & Privacy, but both links
  are `href="#"` and go nowhere. Under GDPR, consent you can't read isn't valid.
- **Tymora** — stores email credentials, call recordings and DMs for 500+ businesses;
  privacy policy is 18 months stale and doesn't cover integrations they've since added.
- **Dhurandhar FanPulse** — a "live" analytics dashboard showing **negative box-office
  revenue** and an impossible buzz score, still running under a real brand.

---

## The Deliverable

A **pre-launch checklist** any AI-site builder can run in 30 minutes to catch every
gap I found — legal pages, exposed data, platform fingerprints, and a final
"click every link before you share it" pass. (Full version in the README.)

---

## What This Project Demonstrates

- **Applied security thinking** — passive reconnaissance, threat framing, and knowing
  where risk clusters, without ever crossing into unauthorized access.
- **Real tooling** — Firecrawl API, scripted discovery/scrape/analyze/score pipeline in
  Node.js + Python, reproducible from the repo.
- **Regulatory literacy** — GDPR, CCPA, COPPA, FERPA applied to concrete cases.
- **Research rigor** — a testable hypothesis (Round 1), a generalization test (Round 2),
  a scoring rubric, and a methodology fix (SPA-aware detection) when the naive approach failed.
- **Communication** — turning 30 audits into a clear, honest, actionable story.

---

## Ethics

Every finding is based on publicly observable information. No authenticated access,
active probing, or exploitation was performed on any site. The goal is to help
builders ship responsibly, not to expose them.

---

## Read More

| Document | What's In It |
|----------|--------------|
| [`README.md`](README.md) | The Round 1 story + the full pre-launch checklist |
| [`ai-built-websites-report.md`](ai-built-websites-report.md) | Deep site-by-site breakdown of the first 10 |
| [`round2-cross-builder-expansion.md`](round2-cross-builder-expansion.md) | The 20-site, 4-builder generalization study |
