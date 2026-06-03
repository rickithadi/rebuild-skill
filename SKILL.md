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
5. Identify 2–3 direct competitors from the reference URL's industry and market. These will be scraped in Phase 1 to inform the design direction with real competitive context rather than generic anti-slop principles.

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

**Logo**
- Look for `<img>` tags in `<header>` or `<nav>` with `alt` text containing the site/brand name, or class/id names like `logo`, `brand`, `site-logo`
- Check `<a href="/">` wrapping an `<img>` — this is almost always the logo
- Check `<meta property="og:logo">` or `<link rel="apple-touch-icon">` as secondary signals
- Extract the absolute URL. If it's an SVG, that's ideal — use it directly. If it's a PNG/WebP, use as-is.
- Save this URL as `Logo` in CONTENT.md. This will be used in the Navbar and footer.

**Favicon**
- `<link rel="icon" href="...">` — the primary favicon (may be `.ico`, `.png`, or `.svg`)
- `<link rel="icon" type="image/svg+xml" href="...">` — SVG favicon, preferred if present
- `<link rel="apple-touch-icon" href="...">` — used as a 180×180 fallback
- `<link rel="manifest" href="...">` — check the manifest for `icons` array entries
- Extract all favicon URLs found. Prefer SVG > PNG 32px+ > ICO.
- Save as `Favicon` in CONTENT.md. Copy the favicon file into `public/` during Phase 2.

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

#### Step 4 — Scrape 2–3 competitors

Using the competitors identified in Phase 0, scrape each homepage:

```
firecrawl_scrape(competitor_url, { formats: ["markdown", "screenshot"] })
```

Extract for each competitor:
- Their primary headline / value proposition
- Their section structure and layout approach
- Their primary CTA and conversion path
- Any design patterns they're using (dark/light, font style, visual weight)

Do NOT copy anything — this is competitive intelligence only. Add to CONTENT.md:

```markdown
## Competitor Analysis
### [Competitor Name] — [url]
Value prop: [their headline]
Layout: [description]
CTA: [their primary CTA]
Design: [notable patterns]
Differentiation opportunity: [what the client can do better]
```

#### Step 5 — Extract conversion goal

From the source site, identify the single most important action the client wants a visitor to take. This is the **primary conversion goal** — it should be answered by the hero, supported by social proof, and directly accessible within 2 scrolls. Add to CONTENT.md:

```markdown
## Conversion Goal
Primary: [e.g. "submit an inquiry form", "book a discovery call", "purchase a product"]
Secondary: [e.g. "read the blog", "follow on Instagram"]
Current path to conversion: [how many clicks from homepage to goal?]
```

This informs every layout decision in Phase 3 — CTA placement, social proof proximity, section ordering.

#### Step 6 — Write CONTENT.md immediately

Compile everything into `CONTENT.md` before touching any code. Save the Firecrawl `screenshot` from Step 1 as the **before** artifact — note its URL or embed it in CONTENT.md under `## Before Screenshot`. This will be used in the PITCH.md before/after comparison.

#### Step 7 — Quality gate

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

**Logo + Favicon** — extract from rawHtml
- Logo URL (from `<header>/<nav>` img, or `<a href="/">` img)
- Favicon URL (from `<link rel="icon">` — prefer SVG variant)
- Apple touch icon URL

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

## Logo
URL: [absolute url to logo image — SVG preferred]
Format: [svg / png / webp]
Notes: [e.g. "dark background only", "has tagline baked in", "white variant available at [url]"]

## Favicon
Primary: [url] — [type: svg / png / ico]
Apple touch icon: [url or "none found"]
Notes: [e.g. "only .ico found — will need SVG recreation", "SVG favicon available"]

## Images
og:image: [url]
Hero: [url] — [subject description]
About/Founder: [url] — [subject description]
[additional images with url and description]
Custom photography: [yes / no / unclear]

## Conversion Goal
Primary: [action]
Secondary: [action]
Current path to conversion: [N clicks from homepage]

## Competitor Analysis
### [Name] — [url]
Value prop: [headline]
Differentiation opportunity: [what client can do better]

## Before Screenshot
[Firecrawl screenshot URL from Phase 1 Step 1]

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
npm install framer-motion lucide-react react-router-dom gray-matter
npm install -D tailwindcss @tailwindcss/vite
```

Configure:
- `vite.config.ts` — add `@tailwindcss/vite` plugin
- `index.html` — full SEO meta tags from `CONTENT.md` (title, description, og:*, twitter:card, canonical). Add Google Fonts `<link>` with `preconnect` AND `font-display=swap` appended to the font URL: `&display=swap`. Add JSON-LD script tag (see `reference/seo-templates.md`). Wire the favicon from `CONTENT.md § Favicon`:
  ```html
  <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
  <link rel="icon" type="image/png" href="/favicon.png" />
  <link rel="apple-touch-icon" href="/apple-touch-icon.png" />
  ```
  Use whichever variants were extracted. If only `.ico` was found, use `<link rel="icon" href="/favicon.ico" />`.
- `public/` — copy the client's favicon file(s) here by fetching the extracted URL(s) with `curl -o public/favicon.[ext] "[url]"`. If SVG favicon found, save as `public/favicon.svg`. If PNG, save as `public/favicon.png`. Also save the logo as `public/logo.[ext]` for use in Navbar/footer `<img src="/logo.[ext]">`. If fetching fails (signed URL, CORS, etc.), note in CONTENT.md § Image Placeholders and use a text wordmark as fallback.
- `src/index.css` — CSS variables for design tokens (see `reference/tech-stack.md`)
- `vercel.json` — SPA rewrite plus security headers:
  ```json
  {
    "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }],
    "headers": [{
      "source": "/(.*)",
      "headers": [
        { "key": "X-Content-Type-Options", "value": "nosniff" },
        { "key": "X-Frame-Options", "value": "DENY" },
        { "key": "Referrer-Policy", "value": "strict-origin-when-cross-origin" }
      ]
    }]
  }
  ```
- `public/robots.txt` — allow all crawlers, reference sitemap:
  ```
  User-agent: *
  Allow: /
  Sitemap: https://[domain]/sitemap.xml
  ```
- `public/sitemap.xml` — static sitemap covering all routes. Update domain placeholder in handoff.
- `src/components/JsonLd.tsx` — structured data component, see `reference/seo-templates.md`
- `src/pages/NotFound.tsx` — branded 404 page with link back to home and primary CTA
- `ITERATIONS.md` — see `reference/iteration-tracking.md`
- `.env.sample` — `VITE_FORMSPREE_ID=your_formspree_form_id`

**Performance defaults to apply from day one:**
- All `<img>` tags: `loading="lazy"` on all below-fold images, `loading="eager"` on hero only
- Unsplash URLs: always append `&fm=webp&q=80` for WebP format and compression
- Client image URLs: use as-is (can't transform external URLs)
- Google Fonts URL: must include `&display=swap` to prevent FOIT

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
Logo: [/logo.svg or /logo.png — confirmed present in public/, or "text wordmark fallback"]
Favicon: [/favicon.svg or /favicon.png — confirmed present in public/]
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

**Conversion-informed layout**: Use `CONTENT.md § Conversion Goal` to position the primary CTA. The hero must contain the primary CTA. Social proof (testimonials) must appear within 2 scrolls of the hero. The contact/booking section must be reachable in no more than 1 click from any page.

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

### Step 2b: JSON-LD structured data

Add the `<JsonLd />` component to `App.tsx` (or the relevant page). Use the site-type template from `reference/seo-templates.md`. Populate all fields from `CONTENT.md` — business name, description, contact details, social profiles, service list.

For service businesses: `LocalBusiness` + `Service` schemas
For SaaS: `SoftwareApplication` schema
For restaurants: `Restaurant` + `Menu` schemas
For professional services: `ProfessionalService` + `Person` schemas

### Step 2c: 404 page

Create `src/pages/NotFound.tsx` — a branded page matching the site's design system. Must include:
- A clear "Page not found" message in the site's voice (not "404 Error")
- A link back to the homepage
- The primary CTA (same as the hero)

Add a catch-all route in `App.tsx`: `<Route path="*" element={<NotFound />} />`

### Step 3: Blog (conditional)

**Only build a blog if the source site has one.** Check `CONTENT.md § Blog Topics` — if it's empty or absent, skip this step entirely. Do not add a blog section, blog pages, or blog routes.

If the source site has a blog:
- Check `CONTENT.md § Navigation` for the source site's blog URL path and use that exactly
- Write 4 original articles aligned to the topics in `CONTENT.md § Blog Topics`
- **Use Markdown files, not hardcoded TypeScript data:**
  - Store posts as `.md` files in `src/content/blog/` with frontmatter: `title`, `date`, `category`, `excerpt`, `slug`
  - Create `src/lib/blog.ts` to read and parse these files using Vite's `import.meta.glob`
  - This means the client can add new posts by creating a `.md` file in GitHub's web UI — no code required
- Add `[/blog-path]` and `[/blog-path]/:slug` routes to `App.tsx`
- Include the Blog preview section on the homepage

```ts
// src/lib/blog.ts — Vite glob import pattern
// Uses gray-matter for frontmatter parsing — handles URLs and colons in values safely
import matter from 'gray-matter'

export interface BlogPost {
  title: string
  date: string
  category: string
  excerpt: string
  slug: string
  content: string
}

const raw = import.meta.glob('../content/blog/*.md', { eager: true, query: '?raw', import: 'default' }) as Record<string, string>

export const posts: BlogPost[] = Object.values(raw).map((src) => {
  const { data, content } = matter(src)
  return { ...data, content } as BlogPost
})
```

### Step 4: Contact form

Formspree with `VITE_FORMSPREE_ID`. States: `idle → sending → sent | error`. Error state must include direct email link as fallback. Never `mailto:` as primary.

### Step 5: Accessibility

- `:focus-visible { outline: 2px solid var(--accent); outline-offset: 3px; }` globally
- Hover via CSS (`a:hover h3`) not `onMouseEnter` on heading elements
- Mobile nav dismissable via backdrop tap or Escape key
- **Reduced-motion**: add to `src/index.css` so Framer Motion animations respect system accessibility settings:
  ```css
  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0.01ms !important;
      animation-iteration-count: 1 !important;
      transition-duration: 0.01ms !important;
    }
  }
  ```
  For Framer Motion components that need finer control, use the `useReducedMotion()` hook and pass `{ duration: 0 }` variants when it returns `true`.

---

## Phase 4 — Git + Vercel Setup

### Step 1 — Create GitHub repo and push

```bash
git init
git add .
git commit -m "Initial build: [site name]"
gh repo create [slug] --public --source=. --remote=origin --push
```

**Branch strategy**: All critique iterations use feature branches, not `main`. `main` only receives the best-scoring iteration at the end.

```bash
# Before each critique iteration:
git checkout -b rebuild/iter-1

# After each iteration commit:
git push origin rebuild/iter-1

# When exit criteria are met — merge only the best iteration to main:
git checkout main
git merge rebuild/iter-N --no-ff -m "Merge best iteration (score N/40)"
git push origin main
```

This means `main` always reflects the best state, and every iteration is individually recoverable.

> **Preview deployments**: Vercel watches all branches by default. Pushing `rebuild/iter-N` will trigger a preview deployment for that branch — this is intentional and useful (each iteration gets its own preview URL). Only the `main` branch deployment is considered the canonical pitch URL. Record both the iteration preview URL and the main URL in `ITERATIONS.md`.

### Step 2 — Create Vercel project and link to GitHub

Create the Vercel project non-interactively and connect it to the GitHub repo so all future `git push` calls auto-deploy:

```bash
GH_USER=$(gh api user --jq .login)
vercel link --yes --scope 9line --project [slug] --repo ${GH_USER}/[slug]
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

### Step 3 — Verify first deployment succeeded

After the initial push, wait ~30 seconds then verify the deployment is live:

```bash
# Get the deployment URL from Vercel
vercel ls --scope 9line 2>/dev/null | head -5
```

Or use the Vercel MCP to check deployment status. Confirm the URL returns HTTP 200 before proceeding to Phase 5. If the deployment failed, check Vercel build logs and fix before continuing.

> **Never run `vercel deploy` after this point.** All deployments happen on `git push`.

---

## Phase 5 — Critique Loop (max 5 iterations, fully autonomous)

**This loop runs without user input.** Do not ask questions, do not pause for confirmation. Run critique → fix everything → commit → push → repeat until exit criteria are met or 5 iterations are exhausted. Then proceed directly to Phase 6.

**Hard cap: 5 iterations.** If score has not reached 36/40 by iteration 5, exit and note the gap in `ITERATIONS.md`.

### Critique mode

Always invoke as `/critique --auto` within this loop. The `--auto` flag is a hard instruction to the critique skill to:
- Skip Phase 3 entirely (no questions, no approval requests, no summaries)
- Implement **all** fixes directly into the working files immediately
- Return only: score, list of what was fixed, list of what was blocked (with reason)

**If the critique skill surfaces a summary or asks a question instead of editing files, treat that as a failure of the `--auto` flag.** In that case: implement every fix from the critique output yourself before proceeding to the build gate. The loop must never stall waiting for user input.

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

5. **Visual verification** — use the `seo-visual` agent (or Playwright directly) to take a screenshot of the built/deployed site at 1440px and 375px. Compare against the Firecrawl screenshot saved in `CONTENT.md § Before Screenshot`. Check:
   - Fonts are rendering (not falling back to system fonts)
   - Hero image is loading
   - Color palette matches extracted brand colors
   - No layout overflow or broken sections visible
   - Text is readable and hierarchy is clear
   Fix any visible issues before committing.

6. **Regression check** — verify that key sections are still present by scanning the built code:
   - Navbar with primary CTA exists
   - Hero section with headline and CTA exists
   - Contact form or booking link exists
   - Footer with contact details exists
   If any are missing, restore them before committing.

7. **Deployment verification** — after pushing, confirm the Vercel deployment succeeded by checking the deployment URL returns HTTP 200. If it fails, check the Vercel build log and fix before marking the iteration complete.

8. Update `ITERATIONS.md` — score, all changes made, build gate results, visual check result, regression check result, Vercel deployment URL

9. `git add . && git commit -m "Iteration N: [summary]" && git push origin rebuild/iter-N`

10. Check exit criteria — if met, merge best iteration to `main` and proceed to Phase 6. If not, create `rebuild/iter-N+1` branch and start next iteration from step 1.

### Exit criteria (all must be true):
- Score ≥ 36/40
- Anti-patterns verdict: PASS
- No unresolved P0 or P1 issues
- Mobile check passed at 375px and 768px
- Visual verification passed (fonts, images, colors rendering correctly)
- Deployment verified live at HTTP 200

### If 5 iterations reached without meeting exit criteria:
- Add `⚠️ MAX ITERATIONS REACHED` entry to `ITERATIONS.md`
- Note final score, remaining issues, and why they're blocked
- Proceed to Phase 6 — do not loop further

---

## Phase 6 — Handoff

Write three files, then output the console summary.

### ITERATIONS.md — final entry

Mark the last iteration `✓ FINAL` and note which branch was merged to `main`. Add:
- Final score and band
- Anti-patterns verdict
- Visual verification result
- Outstanding items: image placeholders, Formspree not configured, pages out of scope

### PITCH.md

Generate from `reference/pitch-template.md`. Enhancements for this version:

**Before/after section**: Include the Firecrawl screenshot URL from `CONTENT.md § Before Screenshot` as the "before" image. Add a note that the "after" is live at the Vercel URL. Frame the comparison around the 3 most visible improvements found in the first critique pass.

**Competitor context**: Add a brief line using `CONTENT.md § Competitor Analysis` — something like: *"We reviewed [Competitor A] and [Competitor B] while designing this. The approach we took differentiates on [specific opportunity]."* This positions the rebuild as research-informed, not just aesthetically improved.

**SEO additions**: Add a bullet noting that structured data (JSON-LD), a sitemap, and robots.txt were added. These are invisible improvements the client's current site likely lacks and are easy wins to highlight.

### MAINTENANCE.md

Generate from `reference/maintenance-template.md`. This is the document that tells the client how to update their own site. It must:
- Explain how to add a blog post (create a `.md` file in `src/content/blog/`)
- Explain how to update contact details (one file to edit)
- Explain how to update services copy (one file to edit)
- Explain how to replace placeholder images
- Explain the Formspree setup with step-by-step instructions
- Note what requires a developer vs. what the client can do themselves

### Console output

```
## ✓ Rebuild Complete — [Site Name]

Source: [original URL]
Repo: https://github.com/[user]/[slug]
Live: [Vercel deployment URL — verified HTTP 200]
Final score: [N]/40
Anti-patterns: PASS

Sections: [list]
Blog articles: [titles]
Structured data: [schema types added]

Client action items:
1. Formspree: formspree.io → New Form → [email] → VITE_FORMSPREE_ID in Vercel env vars
2. Photography: [N] placeholders — see CONTENT.md § Image Placeholders
3. Domain: point [domain] to Vercel — Dashboard → Domains → Add
4. [any out-of-scope pages]

Files for the client:
- PITCH.md — send this to the client
- MAINTENANCE.md — send this when they sign
- ITERATIONS.md — internal build history
```

---

## Rules

1. **Write CONTENT.md before any code.** It survives context compression; your memory does not.
2. **Scrape competitors in Phase 1.** Design direction must be informed by the real competitive context, not generic anti-slop principles alone.
3. **Extract conversion goal in Phase 1.** Every layout decision is downstream of the primary conversion goal.
4. **Use Firecrawl with rawHtml + screenshot.** Extract actual client images, fonts, and colors — don't invent them if they're available.
5. **Use client's actual images, fonts, colors, logo, and favicon.** Only fall back to Unsplash/invented assets when extraction genuinely fails. Use the real logo in the Navbar and footer; use the real favicon in `index.html`. If neither can be fetched, use a text wordmark and note it in Image Placeholders.
6. **Testimonials are verbatim.** Character-for-character. No paraphrasing ever.
7. **Service names, contact details, credentials are verbatim.** Everything else can be improved.
8. **Detect site type before scaffolding.** Use `reference/site-types.md`.
9. **`data-replace` only for unavailable images.** Not for every image — only ones where the client URL failed.
10. **Add JSON-LD, sitemap.xml, robots.txt in Phase 2.** SEO structure is not optional — the client's current site likely lacks it and it's an easy pitch differentiator.
11. **Blog uses Markdown files, not TypeScript data.** Clients must be able to add posts without touching code.
12. **404 page is required.** Add it with a route and branded design before any critique iteration.
13. **No AI-slop patterns.** Passes `/frontend-design` anti-pattern check.
14. **Formspree over mailto.** mailto is fallback only.
15. **Link Vercel in Phase 4.** `vercel link --yes` + `vercel git connect --yes`. Never run `vercel deploy` after that.
16. **Verify deployment after every push.** HTTP 200 on the Vercel URL before marking an iteration complete.
17. **Only build a blog if the source site has one.** No blog in CONTENT.md = no blog in the build.
18. **Blog routes match the source.** Use the source site's exact path — never default to `/journal`.
19. **Branch-per-iteration.** Critique iterations go on `rebuild/iter-N` branches. Only the best-scoring iteration merges to `main`.
20. **Always invoke `/critique --auto` in the loop.** Never call `/critique` without `--auto` inside Phase 5.
21. **Two build gates per iteration.** `npm run build` before critique and after. Never commit a broken build.
22. **Visual verification every iteration.** Screenshot at 1440px and 375px — check fonts, images, colors, layout.
23. **Regression check every iteration.** Verify navbar, hero, contact form, and footer still exist after every set of fixes.
24. **Fix all severity levels.** P0 through P3 — everything gets fixed unless blocked by a missing client asset.
25. **5-iteration cap.** Exit with explanation if not resolved.
26. **Write PITCH.md, MAINTENANCE.md, and ITERATIONS.md at handoff.** Three files, not one.
