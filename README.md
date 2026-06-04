# If AI Builts It. I Audit It's Security. 

I was curious about something: AI builders like Lovable let anyone ship a full-stack web app from a single sentence. That's impressive. But what do they miss?

I picked 10 real (Y just 10, best this is just a sample), live websites from the Lovable community showcase and ran a passive audit on all of them using the Firecrawl API — mapping their URLs, scraping their pages, and analyzing what was publicly visible. No hacking, no login attempts, no active probing. Just reading what the sites were already showing to any visitor.

What I found surprised me. These weren't abandoned prototypes — some of them had hundreds of paying customers and real business data. But the security and compliance gaps were consistent and fixable.

---

## The Sites I Audited

| # | Site | What It Does | Score |
|---|------|-------------|-------|
| 1 | [LumièresPartners](https://lumierespartners.com) | Hospitality consultancy | 7.5 / 10 |
| 2 | [PathCraft](https://roll-a-path.lovable.app) | Browser game | 4 / 10 |
| 3 | [WanderScout](https://wanderscout.app) | AI travel search | 9.5 / 10 |
| 4 | [TrackingPass](https://trackingpass.com) | Loyalty SaaS for local businesses | 7 / 10 |
| 5 | [AgentSwarms](https://agentswarms.fyi) | AI learning platform | 9 / 10 |
| 6 | [Tymora](https://tymora.ai) | AI receptionist for service businesses | 8 / 10 |
| 7 | [CutList Optimizer](https://cutlayout.lovable.app) | Woodworking/CNC tool | 5 / 10 |
| 8 | [GoldMine AI](https://goldmineai.io) | Startup validation platform | 7 / 10 |
| 9 | [Ninja Alert](https://ninja-alert.com) | Sports competition notifications | 6.5 / 10 |
| 10 | [Dhurandhar FanPulse](https://dhurandharpart2insights.lovable.app) | Movie analytics dashboard | 2 / 10 |

---

## How I Did It

```
For each site:

1. MAP    — Firecrawl discovers every public URL
2. SCRAPE — Full page content extracted as markdown
3. CRAWL  — Key sub-pages: /privacy, /terms, /auth, /contact

Then I analyzed for:
- Missing or broken legal pages (privacy policy, terms, cookie consent)
- Passive security signals (exposed emails, broken links, stale data)
- AI builder fingerprints (internal project UUIDs, placeholder href="#" links)
- Data handling issues (children's data, credential storage, outdated policies)
```

**Tools:** Firecrawl API · Node.js + Python

---

## What I Found

### The Numbers

| Issue | How Many |
|-------|----------|
| No privacy policy at all | 4 of 10 |
| No cookie consent notice | 6 of 10 |
| Legal links broken on signup page | 1 (GoldMine AI) |
| Internal project UUID exposed publicly | 3 sites |
| Privacy policy dangerously out of date | 1 (Tymora — 18 months old) |
| Live dashboard showing impossible/negative data | 1 (Dhurandhar) |
| Children's data collected with no privacy policy | 1 (Ninja Alert) |

---

## The Worst Findings

### GoldMine AI — You Can't Read the Terms You're Agreeing To

On their signup page, both "Terms of Service" and "Privacy Policy" are clickable links — but both point to `href="#"`. They go nowhere.

```html
By continuing, you agree to our
<a href="#">Terms of Service</a> and <a href="#">Privacy Policy</a>
```

The actual terms *do exist* at `/terms` and `/privacy`. The AI just generated placeholder links that were never updated. Under GDPR Article 7(2), consent isn't valid if users can't read what they're agreeing to. This is a fixable, 30-second change that nobody caught before launch.

---

### Tymora — 18-Month-Old Privacy Policy for a Credential Handler

Tymora stores email credentials, calendar access, call recordings, voicemail transcripts, and social DM content for 500+ service businesses.

Their privacy policy: last updated **January 2025**.

They've added Vapi voice AI, WhatsApp, and CRM integrations since then — none of it covered in the privacy policy that users are reading today.

---

### Ninja Alert — Children's Athletic Data, No Privacy Policy

Ninja Alert tracks when kids compete in ninja competitions and emails their parents. It collects parent emails and children's names. There's no privacy policy anywhere on the site.

COPPA exists specifically for this scenario. A parent trusting a service with their child's schedule deserves to know what happens to that data.

---

### Dhurandhar FanPulse — Negative Box Office Revenue, Live Right Now

This dashboard is publicly live and showing:

```
Buzz Score:  -348 / 100   ← impossible (scale is 0–100)
Worldwide:   ₹-4470Cr+    ← negative revenue
India Net:   ₹-2825Cr+    ← negative revenue
```

The dashboard even has its own warning: `⚠ Stale — Updated 2 months ago`

The AI-written insight text still says the film is *"a gold standard for Indian spy thrillers"* while the buzz score reads -348. Nobody checked before walking away from it.

---

## What AI Builders Consistently Miss

These fingerprints appeared across multiple sites — they're not bugs, but they signal where gaps tend to cluster:

| Pattern | What It Means | Sites Affected |
|---------|---------------|----------------|
| `Edit with Lovable` badge in footer | Exposes internal project UUID | 3 sites |
| `/lovable-uploads/` in image paths | Reveals build platform and storage path | GoldMine AI |
| `href="#"` on legal links | Unfinished placeholders from AI template | GoldMine AI |
| Hardcoded Unsplash image URLs | External dependency that can silently break | WanderScout |
| Founder email in raw HTML footer | Harvested by spam crawlers within days | LumièresPartners |
| Static data in a "live" dashboard | No pipeline actually connected | Dhurandhar |
| Old privacy policy date | Features added, policy never updated | Tymora |

---

## The Checklist — Before You Activate Your AI-Built Site

This is what I wish every builder in this audit had run through:

```
LEGAL
 [ ] /privacy exists and is linked in the footer
 [ ] /terms exists and is linked in the footer  
 [ ] Privacy policy covers every integration you actually use
 [ ] Privacy policy date is current
 [ ] Cookie notice shows up if you use analytics
 [ ] Legal links on your signup page are NOT href="#"

DATA
 [ ] No personal email addresses rendered as plaintext in HTML
 [ ] Contact forms have spam protection
 [ ] If you handle children's data, you've looked up COPPA
 [ ] If you store passwords or credentials, encryption is documented

PLATFORM
 [ ] "Edit with Lovable" (or equivalent) badge is removed
 [ ] Background images are under your control, not hotlinked
 [ ] You've tested every external dependency

BEFORE YOU SHARE THE LINK
 [ ] Open the site in a browser and click every footer link
 [ ] Check your auth/signup page — do legal links actually open something?
 [ ] If you show live data, test it with real values including edge cases
 [ ] If you show numbers, confirm negative values are handled
```

---

## Best vs Worst

**WanderScout (9.5/10)** was the standout. Their privacy policy is 1,800 words, covers GDPR, UK GDPR, and CCPA by name, specifies exactly what gets sent to the AI API, and distinguishes strictly-necessary cookies from optional analytics. A human clearly wrote it carefully. That effort is the difference.

**Dhurandhar FanPulse (2/10)** is the cautionary case. Great idea, no maintenance, corrupted data left running under a real organization's brand. Worse than having no dashboard at all.

---

## The Main Thing I Took Away

AI builds the UI. You're still responsible for everything around it.

The gaps I found weren't sophisticated — they were things a 30-minute review before launch would have caught. Broken links. Missing pages. A date that wasn't updated. An email address left in the HTML.

Security isn't just about attacks. A lot of it is just paying attention.

---

## Full Report

The complete site-by-site breakdown with detailed evidence, tech stack analysis, and scoring for all 10 sites is in [`ai-built-websites-report.md`](ai-built-websites-report.md).

---

*Audit conducted June 2026. All findings are based on publicly observable information. No authenticated access, active probing, or exploitation was performed.*
