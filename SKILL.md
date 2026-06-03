---
name: rebuild
description: >
  Full website rebuild workflow. Given a reference URL, extracts all copy and content into CONTENT.md, detects site type, scaffolds a modern React + Vite site with editorial-quality design, and runs /critique in a loop (max 5 iterations) until score reaches 36+/40. Produces a client-ready sample site with PITCH.md for the sales conversation. Use when given a URL to rebuild, improve, or redesign a website for a client pitch.
user-invocable: true
argument-hint: "[reference-url]"
---

# Website Rebuild Workflow

You are building a modern, improved, client-ready version of an existing website for a client pitch.

**Goal**: the rebuilt site must be **better in design** but **recognisably theirs in content** — every service name, testimonial, CTA, and credential preserved. Supplementary copy (intros, blog articles, experience descriptions, section taglines) can and should be invented or enhanced to improve the pitch quality.

> **ARGUMENTS**: The reference URL is passed as the skill argument. If none provided, ask for it before proceeding.

---

## Content Hierarchy

### Must be verbatim (never change):
- Testimonials — full quotes, exact client names, trip/project context
- Service names
- Founder name and exact credential strings ("repeat expat, arts, history & culinary expert...")
- Professional memberships (Virtuoso, etc.)
- Contact details — email, phone, social handles, booking URL
- Any pricing or legal copy shown on the source

### Should closely match (editorially equivalent, not word-for-word):
- Hero headline and tagline — preserve the phrase if it's strong; improve it if it's weak
- About/story paragraphs — keep the facts and voice; tighten the prose
- Service descriptions — keep the intent and scope; sharpen the language
- Key brand taglines — if they're memorable, keep them exactly

### Can be invented or enhanced freely:
- Blog article body content (match topics, invent the substance)
- Experience showcase descriptions and captions
- Section intro copy and pull quotes
- FAQ content (unless source has it — then match it)
- Image captions
- Email capture offer description if source is vague

---

## Phase 0 — Read Context

1. Check memory for any existing project notes on this client or URL
2. Invoke `/frontend-design` to load design principles and anti-pattern list
3. Check the working directory — if a `CONTENT.md` already exists, skip Phase 1 and continue from the most recent iteration
4. Confirm Firecrawl MCP is available (`mcp__firecrawl-mcp__firecrawl_scrape`). If not, note that Phase 1 will use WebFetch with reduced fidelity on JS-rendered sites.

---

## Phase 1 — Content Extraction → CONTENT.md

**Do not write a single component before CONTENT.md exists.**

### Extraction method

Use **Firecrawl** as the primary tool. Fall back to WebFetch only if Firecrawl is unavailable.

#### Step 1 — Scrape the homepage with full asset extraction

```
firecrawl_scrape(url, { formats: ["rawHtml", "markdown", "links", "screenshot"] })
```

The `rawHtml` output is where images, fonts, and colors are found. The `screenshot` is used for color analysis. The `markdown` carries the text content. The `links` reveal the site structure.

Check word count of the markdown result: if under ~300 words, the site likely uses heavy JS rendering and Firecrawl may have been partially blocked — note this in CONTENT.md and attempt WebFetch as a cross-reference.

#### Step 2 — Extract visual assets from rawHtml

From the `rawHtml`, extract:

**Images**
- All `<img src="...">` and `srcset` values — keep absolute URLs, discard data URIs and tracking pixels (1x1, under 10px)
- `<meta property="og:image">` — usually the hero or brand image
- CSS `background-image: url(...)` values if present in `<style>` tags
- Identify which images are likely: hero, about/founder, service illustrations, blog thumbnails

Use the client's actual image URLs in the build. Only fall back to Unsplash if a URL returns a 4xx/5xx or is clearly a CDN-signed expiring URL.

**Fonts**
- All `<link href="https://fonts.googleapis.com/...">` tags — copy the full URL verbatim
- Any `@import url(...)` in `<style>` tags pointing to Google Fonts
- `font-family` values in inline styles or `<style>` blocks — extract the family names
- If no Google Fonts are found, note the font stack (e.g. `"Helvetica Neue", Arial, sans-serif`) and find the closest Google Fonts equivalent

Reuse the client's exact fonts in the rebuild. Never invent a font pairing if the source fonts are identifiable.

**Colors**
- `<meta name="theme-color" content="...">` — often the primary brand color
- CSS custom properties (`--color-*`, `--primary`, `--accent`, etc.) in any `<style>` tags
- Dominant colors from the `screenshot` — identify background, text, and accent colors visually
- Any hex/rgb/hsl values in inline styles

Build the CSS token palette from these extracted values. Improve the combinations (contrast, hierarchy) but use the client's actual brand colors as the starting point.

#### Step 3 — Scrape key sub-pages for content

From the `links` in Step 1, identify and scrape:
- `/about` or `/our-story`
- `/services` or `/work` or `/offerings`
- `/faq`
- `/blog` or `/journal` (index only — article titles)
- Any page with substantial content not on the homepage

```
firecrawl_scrape(subpage_url, { formats: ["rawHtml", "markdown"] })
```

Extract additional images and any new font/color references from sub-page `rawHtml` as well.

#### Step 4 — Write CONTENT.md immediately

Compile everything into `CONTENT.md` before touching any code.

#### Step 5 — Quality gate

Before proceeding to Phase 1.5, verify CONTENT.md meets minimum viability:

- [ ] Hero headline present
- [ ] At least 1 service name present
- [ ] At least 1 testimonial present
- [ ] Contact email present
- [ ] At least 3 images extracted OR acknowledged as unavailable

If fewer than 4 of these pass, the extraction is too thin to build from. Do not proceed. Instead:
1. Attempt a second Firecrawl pass with `{ formats: ["rawHtml"] }` on any sub-pages not yet scraped
2. Try WebFetch as a cross-reference on the homepage
3. If still thin after both attempts, output a clear explanation of what was and wasn't found, and halt — do not build on incomplete data

### What to extract:

**Structure**
- Navigation link labels and destination URLs
- Page count — single page or multi-page? List all pages found in `links`.
- Platform detected: WordPress, Webflow, Squarespace, Wix, custom, etc. (visible in page source, meta tags, or link patterns)

**Brand Voice**
- Every tagline and memorable phrase (verbatim)
- Mission statement or brand promise
- Tone: formal/casual, expert/approachable, luxury/accessible — quote evidence

**Hero** — verbatim
- Headline, subheadline
- CTA button labels and URLs

**About / Story** — verbatim facts, paraphrasable prose
- All paragraphs
- Founder name, exact credentials
- Professional memberships, certifications, awards

**Services** — service names verbatim, descriptions editorially equivalent
- Every service name
- Descriptions and feature lists
- CTAs
- Pricing if shown

**Testimonials** — 100% verbatim, no exceptions
- Full quote
- Client name (exact spelling)
- Context (project, location, trip)

**Social Proof**
- Any numbers, stats, or claims
- Awards, press, certifications

**Blog**
- Visible article titles (exact)
- Categories

**Contact**
- Email, phone, social handles (full URLs), booking URL, address

**Email Capture**
- List name, offer description

**Images** — extract actual URLs from rawHtml
- All usable image URLs (hero, about, services, blog thumbnails)
- og:image URL
- Whether photography appears custom or stock

**Fonts** — extract from rawHtml
- Full Google Fonts `<link>` URLs (copy verbatim)
- Font family names found in `<style>` tags or inline styles

**Colors** — extract from rawHtml + screenshot
- `theme-color` meta value
- CSS custom properties from `<style>` tags
- Dominant background, text, and accent colors from screenshot analysis

### CONTENT.md format:

```markdown
# [Site Name] — Content Extraction
Source: [URL]
Extracted: [date]

## Site Type
[Service Business / Hospitality / E-commerce / SaaS / Portfolio / Professional Services]

## Multi-page Scope
In scope: [pages to rebuild]
Out of scope: [pages not being rebuilt — note in handoff]

## Navigation
[list with URLs]

## Brand Voice
Taglines: [verbatim list]
Tone: [description with evidence]

## Hero
Headline: [verbatim]
Subheadline: [verbatim]
Primary CTA: [label] → [url]
Secondary CTA: [label] → [url]

## About
[verbatim paragraphs]
Founder: [name], [exact credential string]
Credentials: [list]

## Services
### [Service Name]
Description: [verbatim or close paraphrase]
Features: [list]
CTA: [label] → [url]

## Testimonials
### [Client Name] — [context]
"[full verbatim quote]"

## Social Proof
[list]

## Blog Topics
[exact titles or categories found]

## Contact
Email: [address]
Phone: [if present]
Social: [handle] → [url] (repeat per platform)
Booking: [url]

## Email Capture
List: [name]
Offer: [description]

## Fonts
Google Fonts URL: [full <link> href verbatim, or "none found"]
Font families: [list of font-family names extracted]
Fallback stack: [e.g. "Helvetica Neue", Arial, sans-serif]

## Colors
theme-color: [hex or "not set"]
CSS variables: [list any --color-* or --primary etc. found]
Dominant colors (from screenshot):
  Background: [hex]
  Text: [hex]
  Accent: [hex]
  Secondary: [hex if present]

## Images
og:image: [url]
Hero: [url] — [subject description]
About/Founder: [url] — [subject description]
[additional images with url and description]
Custom photography: [yes / no / unclear]

## Image Placeholders
[filled in during Phase 3 — only for images where the source URL failed or was unavailable]
```

---

## Phase 1.5 — Site Type Detection

Classify the site using `CONTENT.md`. Consult `reference/site-types.md` for the section template and default aesthetic direction that match. Record the type in `CONTENT.md`.

Do not default every site to the Service Business template. A hotel, a SaaS app, a law firm, and a restaurant all need different section structures and different design directions.

---

## Phase 2 — Project Scaffold

```bash
npm create vite@latest [project-slug] -- --template react-ts
cd [project-slug]
npm install
npm install framer-motion lucide-react react-router-dom
npm install -D tailwindcss @tailwindcss/vite
```

Configure:
- `vite.config.ts` — add `@tailwindcss/vite` plugin
- `index.html` — full SEO meta tags from `CONTENT.md` (title, description, og:*, twitter:card, canonical). Add Google Fonts with `preconnect`.
- `src/index.css` — CSS variables for design tokens (see `reference/tech-stack.md`)
- `vercel.json` — `{ "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }`
- `ITERATIONS.md` — see `reference/iteration-tracking.md`
- `.env.sample` — `VITE_FORMSPREE_ID=your_formspree_form_id`

---

## Phase 3 — Build

### Step 1: Declare design direction

Before any code, write this block in the chat (not in a file):

```
Site type: [from CONTENT.md]
Aesthetic: [from reference/site-types.md]
Palette (from CONTENT.md § Colors):
  --bg: [extracted or derived hex]
  --fg: [extracted or derived hex]
  --accent: [extracted or derived hex]
Fonts (from CONTENT.md § Fonts):
  Display: [extracted font family]
  Body: [extracted font family]
Google Fonts URL: [verbatim from CONTENT.md, or new URL if none found]
Sections: [ordered list from site-type template]
```

**Using extracted assets:**
- **Colors**: start from `CONTENT.md § Colors`. Improve contrast and hierarchy but keep the client's brand colors. Only invent a palette if nothing was extractable.
- **Fonts**: use the Google Fonts URL from `CONTENT.md § Fonts` verbatim in `index.html`. Only choose new fonts if none were found.
- **Images**: use URLs from `CONTENT.md § Images` directly in `<img src>`. No `data-replace` needed for images with working URLs.

The design direction must still:
- Be distinct from the source site's *layout and structure* (you are improving the design, not copying it)
- Pass the `/frontend-design` anti-pattern check
- Use the client's actual visual identity (their colors, their fonts, their photos) as the foundation

### Step 2: Build sections

Use the section order from `reference/site-types.md` for the detected type. Pull all fixed content from `CONTENT.md`. Invent supplementary copy (section intros, experience descriptions, blog body text) freely, in the brand voice.

**Images — use client URLs first:**

```tsx
// Client image — URL extracted from source site
<img
  src="[url from CONTENT.md § Images]"
  alt="[descriptive alt text]"
/>
```

Only use an Unsplash fallback when a client image URL is unavailable, returns an error, or is clearly an expiring signed CDN URL. In that case use `data-replace` and log it to `CONTENT.md § Image Placeholders`:

```tsx
{/* TODO: replace with client photography — [what this image should show] */}
<img
  src="https://images.unsplash.com/photo-[id]?w=800&q=85&auto=format"
  data-replace="[description]"
  alt="[descriptive alt text]"
/>
```

### Step 3: Blog (conditional)

**Only build a blog if the source site has one.** Check `CONTENT.md § Blog Topics` — if it's empty or absent, skip this step entirely. Do not add a blog section, blog pages, or blog routes.

If the source site has a blog:
- Check `CONTENT.md § Navigation` for the source site's blog URL path (e.g. `/blog`, `/journal`, `/news`, `/insights`) and use that exact path. Only use `/journal` if the source site uses that word.
- Read the article titles from `CONTENT.md`
- Write 4 original articles aligned to those topics — invent the body content, match the categories, write in the brand voice
- Store in `src/data/blog.ts`
- Add `[/blog-path]` and `[/blog-path]/:slug` routes to `App.tsx`
- Include the Blog preview section on the homepage

### Step 4: Contact form

Formspree with `VITE_FORMSPREE_ID`. States: `idle → sending → sent | error`. Error state must include direct email link as fallback. Never `mailto:` as primary.

### Step 5: Accessibility

- `:focus-visible { outline: 2px solid var(--accent); outline-offset: 3px; }` globally
- Hover via CSS (`a:hover h3`) not `onMouseEnter` on heading elements
- Mobile nav dismissable via backdrop tap or Escape key

---

## Phase 4 — Git + Vercel Setup

### Step 1 — Create GitHub repo and push

```bash
git init
git add .
git commit -m "Initial build: [site name]"
gh repo create [slug] --public --source=. --remote=origin --push
```

### Step 2 — Create Vercel project and link to GitHub

Create the Vercel project non-interactively and connect it to the GitHub repo so all future `git push` calls auto-deploy:

```bash
vercel link --yes --project [slug] --repo rickithadi/[slug]
```

Where `[slug]` matches the GitHub repo name created in Step 1. The `--yes` flag accepts all prompts non-interactively.

Then connect the GitHub integration so pushes to `main` trigger deployments:

```bash
vercel git connect --yes
```

If `vercel git connect` fails (older CLI versions may not support it), fall back to the Vercel MCP if available:

```
mcp__plugin_vercel_vercel — link project to GitHub repo
```

Otherwise note in the console output:
```
⚠️ Vercel GitHub integration needs manual setup:
   Dashboard → [project] → Settings → Git → Connect Repository → [repo name]
```

After linking, commit `.vercel/project.json` and push to trigger the first auto-deployment:

```bash
git add .vercel
git commit -m "Link Vercel project"
git push origin main
```

> **Never run `vercel deploy` after this point.** All deployments happen on `git push origin main`.

---

## Phase 5 — Critique Loop (max 5 iterations, fully autonomous)

**This loop runs without user input.** Do not ask questions, do not pause for confirmation. Run critique → fix everything → commit → push → repeat until exit criteria are met or 5 iterations are exhausted. Then proceed directly to Phase 6.

**Hard cap: 5 iterations.** If score has not reached 36/40 by iteration 5, exit and note the gap in `ITERATIONS.md`.

### Critique mode

Always invoke as `/critique --auto` within this loop. The `--auto` flag is a hard instruction to the critique skill to skip Phase 3, implement all fixes directly, and return only a score + diff summary. This is the mechanism that makes the loop non-interactive — it is not a soft suggestion.

### Each iteration:

1. **Build gate (pre-critique)** — run `npm run build 2>&1`. If it fails, fix all TypeScript and compilation errors before anything else. Do not critique broken code — the deployed site won't render and critique results will be meaningless.

2. Run `/critique --auto` — the `--auto` flag skips Phase 3 questions and directly implements all fixes, outputting only: score, fixed list, blocked list.

3. **Build gate (post-critique)** — run `npm run build 2>&1` again. Critique edits can introduce new TypeScript errors. Fix any that appear before committing.

4. **Mobile check** — verify at 375px and 768px by reading the built code:
   - Nav hamburger present and dismissable (Escape key or backdrop click)
   - No section padding collapses to zero or causes horizontal overflow
   - Images maintain aspect ratios
   - Form inputs and buttons have adequate touch target size (min 44px height)
   - No horizontal scroll at any viewport width
   Fix any issues found.

5. Update `ITERATIONS.md` — score, all changes made, both build gate results, mobile check result, Vercel URL once available

6. `git add . && git commit -m "Iteration N: [summary of all fixes]" && git push origin main`

7. Check exit criteria — if met, proceed to Phase 6. If not, start the next iteration immediately from step 1.

### Exit criteria (all must be true):
- Score ≥ 36/40
- Anti-patterns verdict: PASS
- No unresolved P0 or P1 issues
- Mobile check passed at 375px and 768px

### If 5 iterations reached without meeting exit criteria:
- Add `⚠️ MAX ITERATIONS REACHED` entry to `ITERATIONS.md`
- Note final score, remaining issues, and why they're blocked
- Proceed to Phase 6 — do not loop further

---

## Phase 6 — Handoff

Write these two files, then output the console summary.

### ITERATIONS.md — final entry

Mark the last iteration `✓ FINAL`. Add:
- Final score and band
- Anti-patterns verdict
- Outstanding items: image placeholders needing real photos, Formspree not yet configured, any pages that were out of scope

### PITCH.md

Generate from `reference/pitch-template.md`. This is the document that goes to the client. Plain language, no technical jargon, before/after framing.

### Console output

```
## ✓ Rebuild Complete — [Site Name]

Source: [original URL]
Repo: https://github.com/[user]/[slug]
Final score: [N]/40
Anti-patterns: PASS / FAIL

Sections built: [list]
Articles written: [titles]

Client action items:
1. Formspree: formspree.io → New Form → [email] → VITE_FORMSPREE_ID in Vercel env
2. Photography: [N] placeholders — see CONTENT.md § Image Placeholders
3. [any out-of-scope pages noted]

PITCH.md is ready for the client email.
ITERATIONS.md has the full build history.
```

---

## Rules

1. **Write CONTENT.md before any code.** It survives context compression; your memory does not.
2. **Use Firecrawl with rawHtml + screenshot.** Extract actual client images, fonts, and colors — don't invent them if they're available.
3. **Use client's actual images, fonts, and colors.** Only fall back to Unsplash/invented assets when extraction genuinely fails.
4. **Testimonials are verbatim.** Character-for-character. No paraphrasing ever.
5. **Service names, contact details, credentials are verbatim.** Everything else can be improved.
6. **Detect site type before scaffolding.** Use `reference/site-types.md`.
7. **`data-replace` only for unavailable images.** Not for every image — only ones where the client URL failed.
8. **No AI-slop patterns.** Passes `/frontend-design` anti-pattern check.
9. **Formspree over mailto.** mailto is fallback only.
10. **Link Vercel in Phase 4.** Run `vercel link` + `vercel git connect` so all pushes auto-deploy. Never run `vercel deploy` directly after that.
11. **Only build a blog if the source site has one.** No blog in CONTENT.md = no blog in the build.
12. **Blog routes match the source.** `/blog`, `/news`, `/insights` — whatever the source uses. Never default to `/journal` unless the source uses that word.
13. **Always invoke `/critique --auto` in the loop.** The flag is the hard mechanism for autonomy — not a text instruction. Never call `/critique` without `--auto` inside Phase 5.
14. **Two build gates per iteration.** `npm run build` before critique and after. Never commit a broken build.
15. **Fix all severity levels.** P0 through P3 — everything gets fixed unless blocked by a missing client asset.
16. **5-iteration cap.** Exit with explanation if not resolved.
17. **Explicit mobile check every iteration.** 375px and 768px before committing.
18. **PITCH.md at handoff.** The rebuild only sells if the client can read about it.
19. **ITERATIONS.md stays current.** Every push gets an entry.
