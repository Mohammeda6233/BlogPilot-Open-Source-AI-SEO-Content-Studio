# BlogPilot AI — Complete Usage Guide

This is the deep-dive walkthrough of every feature. For a 60-second quick start, see [README.md](README.md#-quick-start).

**Default URL after launch:** http://localhost:44321

---

## Table of contents

1. [First-time setup](#1-first-time-setup)
2. [Configure an AI provider](#2-configure-an-ai-provider-optional)
3. [Add a client](#3-add-a-client-the-foundation-step)
4. [Train brand voice](#4-train-brand-voice-recommended)
5. [Keyword research](#5-keyword-research)
6. [Generate content calendar](#6-generate-content-calendar)
7. [Outline a post](#7-outline-a-post)
8. [Write the draft](#8-write-the-draft)
9. [Score against the SERP](#9-score-against-the-serp-the-surfer-killer)
10. [Refresh existing posts](#10-refresh-an-existing-post)
11. [Run a technical SEO audit](#11-run-a-technical-seo-audit)
12. [Use the SEO tools hub](#12-use-the-seo-tools-hub-paa-tree--serp-features--topic-authority)
13. [Track ranks + import GSC/GA4](#13-track-ranks--import-gsc--ga4)
14. [Distribute the post](#14-distribute-social-newsletter-cms-export-share-link)
15. [Schedule publishing](#15-schedule-publishing)
16. [Hreflang for multi-language](#16-multi-language-hreflang)
17. [Generate llms.txt](#17-generate-llmstxt-for-ai-search)
18. [Troubleshooting](#troubleshooting)

---

## 1. First-time setup

### Install

**macOS / Linux**
```bash
git clone https://github.com/IamRamgarhia/blogpilot-ai
cd blogpilot-ai
./install.sh
```

**Windows**
```powershell
git clone https://github.com/IamRamgarhia/blogpilot-ai
cd blogpilot-ai
.\install.ps1
```

The installer:
- Checks your Node version (needs ≥ 18.17)
- Installs npm dependencies
- Creates `./data/` for your local SQLite database
- Copies `.env.example` → `.env`
- Generates database migrations
- **Creates a "BlogPilot AI" shortcut on your Desktop**

### Launch

**Three equivalent ways:**

| Method | What it does |
|---|---|
| Double-click the **BlogPilot AI** Desktop shortcut | Boots the server + opens browser automatically |
| `./start.sh` (macOS/Linux) or `.\start.ps1` (Windows) | Same |
| `npm run launch` | Same |

All three start the server on **port 44321** (chosen to never collide with other software) and open your default browser at http://localhost:44321.

### Stop

```bash
npm run stop
```

Cross-platform — works on Windows / macOS / Linux. Kills whatever is on port 44321.

### Reset to a clean state

```bash
npm run clean       # removes .next, data/, .launcher, test artifacts
npm run setup       # recreates them fresh
```

---

## 2. Configure an AI provider (optional)

BlogPilot works **without any AI key** — every generator has a deterministic fallback path. But adding even a free Gemini key dramatically improves output quality.

### Step-by-step

1. Open http://localhost:44321/settings
2. Pick a provider (Google Gemini recommended — 1,500 free requests/day)
3. Click **Get key** — opens the provider's sign-up page in a new tab
4. Copy your API key
5. Open `.env` in the project root, paste the key after the matching env var name:
   ```
   GEMINI_API_KEY=your-key-here
   ```
6. Save `.env`
7. Restart BlogPilot:
   ```
   npm run stop
   npm run launch
   ```
8. Back on the Settings page, click **Test** next to your provider — should report "✓ reachable"

### Multiple providers + auto-failover

You can set as many provider keys as you want. BlogPilot tries them in priority order and falls over to the next if one fails or rate-limits.

Priority order (lowest priority value = tried first):
1. Anthropic (5)
2. Gemini (10)
3. OpenAI (15)
4. Groq (20)
5. DeepSeek (25)
6. OpenRouter (30)
7. Together (35)
8. Mistral (40)
9. Cerebras (45)
10. Perplexity (50)
11. LM Studio (60)
12. Ollama (90)

---

## 3. Add a client — the foundation step

Every workflow in BlogPilot starts with a client.

### Step-by-step

1. From the home page, click **+ Add client** (the blue button in the hero)
2. Paste any website URL — your own site, a client's site, or a competitor's
   - Format: `https://example.com` or just `example.com` (the prefix is auto-added)
3. Click **Add and auto-discover**
4. Wait 30-60 seconds while BlogPilot:
   - Fetches the homepage with Playwright (or Cheerio fallback)
   - Extracts identity (title, description, language, generator)
   - Parses the sitemap
   - Detects social profiles
   - Calls PageSpeed Insights v5 for real CrUX Core Web Vitals
   - Saves a discovery snapshot
5. You'll land on the **Client Dashboard** showing four cards:
   - **Identity** — title, description, language, CMS, social URLs
   - **Sitemap** — URL count + first 20 URLs
   - **Core Web Vitals** — performance / LCP / INP / CLS (mobile-first)
   - **Social profiles** — auto-detected from outbound links

### From the dashboard you can:

- **Research** keywords for this client
- View / generate the **Calendar**
- Run **Competitors** analysis (gap + scanner)
- **Train voice** with sample posts
- **Refresh post** (paste an old URL → AI rewrites)
- **Schedule** publishing dates
- **Measure** rank / decay / recommender
- Open **Tools** (PAA / SERP / Authority)
- Run **Audit** (technical SEO)
- Manage **Hreflang**
- Download **llms.txt**

---

## 4. Train brand voice (recommended)

The auto-discovery extracts a basic style profile, but pasting 3-5 of the client's best posts gives the AI writer a much richer voice to match.

### Step-by-step

1. From the client dashboard, click **Train voice**
2. Paste 3-5 published blog posts (markdown OR plain text) into the textareas
   - Each sample should be ≥ 100 characters
   - Click **+ Add another sample** for slots 4 and 5
3. Click **Train voice**
4. After 5-30 seconds you'll see the extracted profile JSON:
   - Tone (formal / conversational / technical / playful)
   - Voice (first-person / second-person / third-person)
   - Average sentence length
   - Heading case (title / sentence)
   - CTA style
   - Vocabulary level
   - Hedge words usage
   - Contractions usage
   - Em-dash usage
   - Lists vs prose ratio
   - Author persona
   - Phrases to keep / avoid

5. The profile is saved to the client. Every future post written for this client will match this voice.

---

## 5. Keyword research

### Step-by-step

1. From client dashboard, click **Research**
2. Enter a seed keyword (e.g., "wordpress seo")
3. Click **Research**
4. After 5-8 seconds, you'll see:
   - **Keyword candidates table** — 25 suggestions from Google Autocomplete + PAA, each tagged with intent (informational / commercial / transactional / navigational) + recommended format (long-form-guide / listicle / comparison-table / tutorial)
   - **Bing SERP top-10** — the actual pages ranking, with titles + URLs
5. Check the boxes next to keywords you want to write about
6. Click **Generate calendar from selected →**

You'll land on the Calendar page with one post created per selected keyword.

### Tips

- Start with 1-3 seed keywords; you can run multiple research sessions per client
- Bing's SERP closely tracks Google's for most niches and doesn't need an API key
- Pick a mix of intent types so your cluster covers the full funnel

---

## 6. Generate content calendar

After research → calendar generation runs automatically. You can also click **Calendar** from the client dashboard at any time.

### What you see

A Kanban-style board with 4 columns:

| Column | Meaning |
|---|---|
| **Idea** | Just generated, no outline yet |
| **Outline approved** | Outline exists and was approved by you |
| **Draft** | Full post written |
| **Ready** | You marked it as final |

### Step-by-step

1. Click any **Idea** card to open its outline page
2. Click any **Draft** card to open the editor

---

## 7. Outline a post

### Step-by-step

1. Click an Idea card on the calendar
2. The outline page auto-generates an outline within 4-8 seconds (using the AI executor + outline-structure / skyscraper-technique / featured-snippet-targeting / paa-optimization methodologies)
3. Review the left pane:
   - **Title**
   - **TL;DR bullets** (3-5)
   - **Intro hook**
   - **Body** (H2 sections with H3 sub-points, marked if targeting a featured snippet)
   - **FAQs** (4-6 questions)
   - **Conclusion CTA**
   - **Word count target**
4. If you want to edit, modify the JSON in the right pane
5. Click **Approve & write full post →**

The page redirects to the draft view as the AI writes (12-25s).

---

## 8. Write the draft

The draft page shows:

### Left pane — Markdown draft
The full post. Edit it directly here.

### Right sidebar
- **Meta** — title (char-counted to 60) + description (char-counted to 160)
- **SEO checks** — 10 deterministic rules (H1 present, keyword in H1, keyword in intro, meta lengths, schema present, FAQ section present, min word count, FK grade, passive voice)
- **Internal link suggestions** — auto-ranked across all client posts
- **Image briefs** — alt text + AI generation prompts per H2 section (click Generate to populate)
- **Readability** — Flesch-Kincaid grade, reading ease, sentence length, passive voice %

### Top-right buttons
- Download .md
- Download .html
- Download .json
- **Score** — opens the live content score editor
- **Distribute** — opens social / newsletter / CMS export / share link

---

## 9. Score against the SERP — the Surfer killer

This is the headline feature. Live TF-IDF grading vs top-10 SERP terms.

### Step-by-step

1. From any draft page, click **Score**
2. Enter the **target keyword** (the keyword the post is competing for)
3. Click **Load SERP corpus**
4. Wait 15-30 seconds while BlogPilot:
   - Scrapes Bing top-10
   - Fetches the body text of each page
   - Computes TF-IDF + n-gram frequencies
   - Identifies "required" terms (in 5+ competitors) and "recommended" terms (in 3-4)
5. The grade card appears (A through F + 0-100 score)
6. Start editing the draft. **Every 800ms of inactivity, the score updates.**

### What to watch

- **Required terms** chips — green chips are in your draft; gray chips are missing
- **Word count bar** — green if within 0.7× to 1.5× of SERP median
- **Heading parity** — does your H2 set cover the same topics top-3 competitors do?
- **PAA coverage** — answered questions from the actual PAA box
- **Over-optimized warnings** (orange) — terms you've used 2.5× more than the median

### Tips

- Aim for grade **A or B** before publishing
- If you see an orange "over-optimized" callout, soften that phrase — natural prose beats keyword density
- Heading parity is harder to fix than terms; restructure your H2s if the score is stuck

---

## 10. Refresh an existing post

For posts already published that are losing traffic or going stale.

### Step-by-step

1. From client dashboard, click **Refresh post**
2. Paste the published URL of the post you want to refresh
3. Click **Refresh post**
4. Wait 20-90 seconds while BlogPilot:
   - Fetches the URL (Playwright, falls back to Cheerio)
   - Extracts the article body
   - Calls AI with `content-refresh-rules` + `eeat-checklist` + `readability-targets` methodologies
   - Updates year references, regenerates meta + schema, tightens readability
   - Returns a change log
5. You land on a new Draft page with the refreshed content
6. Review, edit, and download / publish as a new draft

---

## 11. Run a technical SEO audit

Screaming Frog-style recursive crawler with 28 audit rules.

### Step-by-step

1. From client dashboard, click **Audit**
2. Set **max pages** (default 50; increase up to 200 for thorough audits)
3. Click **Start audit**
4. Wait 60-120 seconds for a 50-page crawl
5. The dashboard shows:
   - **5 severity stat cards** (Pages / Critical / High / Medium / Low) — click any to filter
   - **Cannibalization callout** (orange) if duplicate keyword targets found
   - **Per-finding cards** with severity chip, rule ID, message, fix suggestion, URL, evidence
6. Click any severity card to filter findings
7. Click **.csv** to download the full report

### What's checked

| Category | Rules |
|---|---|
| Crawlability | meta noindex, redirect chains, canonical mismatch, mixed content |
| On-page | title (missing/long/short/duplicate), meta description, H1 (missing/multiple), heading skip |
| Content | thin content (< 200 words) |
| Images | missing alt, long alt, no dimensions, non-WebP/AVIF |
| Schema | invalid JSON-LD, missing required fields, missing Article on blog URL |
| Security | HSTS, CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy |

---

## 12. Use the SEO tools hub (PAA tree · SERP features · Topic authority)

### Step-by-step — PAA Tree

1. From client dashboard, click **Tools**
2. Click the **PAA tree** tab
3. Enter a seed (e.g., "wordpress seo")
4. Click **Build tree**
5. Wait 60-90 seconds while BlogPilot recursively scrapes People-Also-Ask
6. View the collapsible 3-level tree (up to ~40 nodes)
7. Each leaf is a candidate FAQ item or future post

### Step-by-step — SERP Features

1. Switch to the **SERP features** tab
2. Enter a target keyword
3. Click **Detect features**
4. The 9-feature grid lights up:
   - Featured snippet · PAA · Shopping · Map pack · Video carousel · Image pack · Knowledge panel · Top stories · X/Twitter
5. Lime border = present; gray = not detected
6. Use the [serp-features-targeting.md](src/lib/methodologies/serp-features-targeting.md) methodology to plan how to capture each present feature

### Step-by-step — Topic Authority

1. Switch to the **Topic authority** tab
2. Enter the niche (e.g., "wordpress seo")
3. Click **Score authority**
4. Wait 5-15 seconds while BlogPilot:
   - Searches Wikipedia for the canonical article
   - Extracts canonical entities (sections + linked entities)
   - Matches against all your client posts
5. View the score (0-100), tier (Strong / Moderate / Surface), covered entities, missing entities with suggested post titles, recommended action

---

## 13. Track ranks + import GSC / GA4

### Track a rank manually

You can call the rank-check API directly:

```bash
curl -X POST http://localhost:44321/api/rank-check \
  -H "content-type: application/json" \
  -d '{"clientId":"<id>","keyword":"your keyword","url":"https://example.com/post"}'
```

(A UI button is on the post draft page.)

### Import GSC CSV

1. Export from [Google Search Console](https://search.google.com/search-console) → Performance → Export
2. From client dashboard, click **Measure**
3. Paste the CSV into the "Search Console import" textarea
4. Click **Import GSC**
5. You'll see "Imported N rows" confirmation
6. Decay alerts will appear automatically if you have 8+ weeks of data

### Import GA4 CSV

1. Export from GA4 Explorations → "Free-form" with columns Date / Page path / Sessions / Users / Bounce rate / Avg session duration
2. Same Measure page — paste into GA4 textarea
3. Click **Import GA4**

### Decay alerts

After GSC import, BlogPilot computes 4-week trailing average vs prior 4-week:

| Severity | Trigger | Recommended action |
|---|---|---|
| Critical | Position dropped > 10 | Rewrite (intent shift) |
| High | Position dropped > 3 AND impressions down > 15% | Refresh |
| High | Impressions down > 30% | Refresh |
| High | Clicks down > 25% with stable impressions | Rewrite meta title + description |
| Medium | Position dropped > 5 | Refresh |

### What to write next

The Measure page also shows a priority queue of posts to write next, scored by:
- Quick win (currently ranking 11-30 = "page 2-3 push")
- Pillar dependency (cluster missing a pillar with 3+ spokes ready)
- Decay rescue (refresh recommended)
- Gap coverage (filling a competitor gap)

---

## 14. Distribute · social, newsletter, CMS export, share link

### Step-by-step

1. From any draft page, click **Distribute →**
2. **Generate social** → wait 5-10s for native variants:
   - X / Twitter thread (5-7 tweets)
   - LinkedIn post (1300-2000 chars)
   - Instagram caption + hashtags
   - Pinterest description
   - WhatsApp / Telegram broadcast
3. **Generate newsletter** → short (240-char) + long (200-word) versions
4. **Download CMS export** — 4 platform buttons:
   - WordPress XML (Yoast meta + JSON-LD embedded)
   - Ghost JSON
   - Webflow CSV
   - Hugo (TOML front-matter)
5. **Create share link** — 30-day signed read-only URL for client review (no login required)

---

## 15. Schedule publishing

### Step-by-step

1. From client dashboard, click **Schedule**
2. Click **Auto-schedule unscheduled**
3. BlogPilot assigns each unscheduled post a publish date based on:
   - Detected niche (B2B = Tue/Thu mornings; food = Fri/Sun late mornings; etc.)
   - Pillar before spoke ordering
   - First post lands 2 business days from today (review buffer)
4. Review the table; each post shows assigned publish datetime
5. Posts publish on their assigned dates (you still hit "publish" in your CMS — BlogPilot doesn't auto-publish)

---

## 16. Multi-language hreflang

For sites with translated content.

### Step-by-step

1. From client dashboard, click **Hreflang**
2. Click **+ Add variant** for each language/region variant
3. Pick the language from the dropdown (31 BCP 47 presets including `x-default`)
4. Paste the absolute URL for that variant
5. Pick output format:
   - HTML `<link>` tags (for `<head>`)
   - XML sitemap `<xhtml:link>` (for sitemap.xml)
6. Click **Build & validate**
7. Errors (red) — invalid BCP 47, duplicate codes, non-absolute URLs
8. Warnings (yellow) — missing x-default (recommended)
9. Copy the output tags into your site

---

## 17. Generate llms.txt for AI search

`/llms.txt` is a spec-compliant text file at the root of a site that helps AI crawlers (ChatGPT, Perplexity, Claude, Google AI Overviews) find your best content efficiently.

### Step-by-step

1. From client dashboard, click **llms.txt** (opens in new tab)
2. BlogPilot generates a spec-compliant file:
   - H1 = site name
   - Blockquote description
   - `## Blog` section with up to 50 of your drafted posts
   - `## Site` section with homepage
3. Save the file as `llms.txt` at the root of the client's site (e.g., `https://example.com/llms.txt`)
4. For the full version with concatenated post bodies, add `?full=1` to the URL — saves as `llms-full.txt`

---

## Troubleshooting

### Port 44321 already in use
Another instance of BlogPilot is probably running. Run `npm run stop`. If that doesn't work, find the process via your OS task manager and kill it.

### Browser didn't auto-open
The launcher tries Windows `start`, macOS `open`, Linux `xdg-open` in turn. If none are available, manually visit http://localhost:44321.

### "Database failed to open" error
Run `npm run setup`. If still failing, delete `./data/` and re-run setup.

### AI provider returns 429 (rate limit)
Add a second provider key in `.env`. Auto-failover will use the next configured provider when one hits a limit.

### Content score won't update
Make sure you clicked **Load SERP corpus** first. The score only computes once a corpus is loaded. If Bing returns 0 results, try a more specific keyword.

### Audit findings show "private/local address" error
You tried to audit a URL like `http://localhost:8000` — the SSRF guard blocks private addresses for security. Use a public URL.

### Desktop shortcut didn't appear
Run `node ./bin/desktop-shortcut.mjs` manually. If your Desktop folder is at a non-standard location (corporate Windows / OneDrive redirect), the script falls back gracefully with a "skipped" message.

### Tests fail after edit
Run `npm test` first to see specifics. If only the e2e tests are skipping, that's expected — set `BLOGPILOT_E2E=1` and have the dev server running on port 44321 to enable them.

---

## More resources

- [README.md](README.md) — features overview + screenshots
- [DEPLOY.md](DEPLOY.md) — Vercel + Docker + production deployment
- [CONTRIBUTING.md](CONTRIBUTING.md) — how to add methodologies, AI providers, exporters
- [CHANGELOG.md](CHANGELOG.md) — version history
- [docs/specs/](docs/specs/) — design spec
- [docs/promotion/AWESOME_LISTS.md](docs/promotion/AWESOME_LISTS.md) — where to share your project

---

## Built by Dice Codes

Need a custom SaaS like BlogPilot for your industry?

📱 [WhatsApp +91 9888404991](https://wa.me/919888404991) · 📧 [Contact@dicecodes.com](mailto:Contact@dicecodes.com) · 🌐 [dicecodes.com](https://dicecodes.com)
