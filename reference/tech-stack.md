# Tech Stack Reference

## Dependencies

| Tool | Purpose | Required? |
|------|---------|-----------|
| `mcp__firecrawl-mcp__firecrawl_scrape` | JS-rendered content extraction, asset extraction, screenshot | Preferred — fall back to WebFetch if unavailable |
| `gh` CLI (authenticated) | Create GitHub repo in Phase 4 | Required for Phase 4 |
| `vercel` CLI (authenticated) | Link project to Vercel in Phase 4 (`vercel link`, `vercel git connect`) | Required for Phase 4 auto-deploy setup |
| `node` / `npm` | Scaffold and build | Required |
| `git` | Version control | Required |
| Formspree account | Contact form backend | Client sets up post-handoff |

## Standard Stack

| Layer | Choice | Notes |
|-------|--------|-------|
| Framework | React 18+ | Functional components only |
| Build | Vite 6+ | Fast HMR, `@tailwindcss/vite` plugin |
| Styles | Tailwind CSS v4 | Via `@tailwindcss/vite`, `@import "tailwindcss"` in CSS |
| Animation | Framer Motion | `useInView` for scroll triggers, `Variants` for stagger |
| Icons | lucide-react | Check actual exports — Instagram/LinkedIn not available, use AtSign/ExternalLink |
| Routing | react-router-dom v6 | BrowserRouter, `vercel.json` SPA rewrite required |
| Forms | Formspree | Async fetch, `VITE_FORMSPREE_ID` env var |
| Types | TypeScript | Strict, no `any` |

## CSS Token Structure

```css
@import "tailwindcss";

@layer base {
  :root {
    /* Core palette — derive from brand personality, never use navy+gold defaults */
    --bg: #f6efe3;        /* warm ivory */
    --bg-2: #ede4d0;      /* slightly deeper warm */
    --bg-dark: #1c1410;   /* deep warm black for footer/accents */
    --fg: #1c1410;        /* primary text */
    --fg-2: #5c4e3a;      /* secondary/muted text */
    --accent: #b87d3a;    /* brand accent (earthy amber) */
    --accent-light: #d4a060;
    --border: #d0c4b0;

    /* Typography */
    --serif: 'Cormorant Garamond', Georgia, serif;
    --sans: 'Jost', 'Helvetica Neue', Arial, sans-serif;
  }
  html { scroll-behavior: smooth; }
  body { font-family: var(--serif); background: var(--bg); color: var(--fg); }
  section { scroll-margin-top: 72px; }
  :focus-visible { outline: 2px solid var(--accent); outline-offset: 3px; }
  a:hover h3, a:focus h3 { color: var(--accent); }
}
```

## Font Pairings (always load via Google Fonts in `index.html`)

| Display | Body/UI | Aesthetic |
|---------|---------|-----------|
| Cormorant Garamond | Jost | Luxury, editorial |
| Playfair Display | DM Sans | Fashion, lifestyle |
| DM Serif Display | Inter | Modern editorial |
| Libre Baskerville | Raleway | Classic professional |

```html
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;1,300;1,400&family=Jost:wght@300;400;500&display=swap" rel="stylesheet" />
```

## Framer Motion Patterns

```tsx
// Scroll-triggered fade
const ref = useRef(null)
const inView = useInView(ref, { once: true, margin: '-80px' })

<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={inView ? { opacity: 1, y: 0 } : {}}
  transition={{ duration: 0.8, ease: [0.22, 1, 0.36, 1] }}
/>

// Stagger children — use Variants, cast ease as tuple
const stagger: Variants = {
  hidden: {},
  show: { transition: { staggerChildren: 0.12, delayChildren: 0.3 } },
}
const item: Variants = {
  hidden: { opacity: 0, y: 28 },
  show: { opacity: 1, y: 0, transition: { duration: 0.9, ease: [0.22, 1, 0.36, 1] as [number,number,number,number] } },
}
```

**TypeScript gotcha**: framer-motion `ease` with a number array must be cast:
```ts
ease: [0.22, 1, 0.36, 1] as [number, number, number, number]
```

## Vercel Config

```json
// vercel.json — required for SPA routing
{ "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }
```

## Performance Defaults

Apply these from day one — not as an afterthought:

```tsx
// Hero image — eager load, no lazy
<img src="..." loading="eager" fetchpriority="high" alt="..." />

// All other images — lazy load
<img src="..." loading="lazy" alt="..." />

// Unsplash images — always force WebP + compression
src="https://images.unsplash.com/photo-[id]?w=800&q=80&fm=webp&auto=format"

// Google Fonts — always include display=swap
<link href="https://fonts.googleapis.com/css2?family=...&display=swap" rel="stylesheet" />
```

## Markdown Blog Pattern

```ts
// src/lib/blog.ts
export interface PostMeta {
  title: string
  date: string
  category: string
  excerpt: string
  slug: string
}

// Vite glob import — reads all .md files at build time
const modules = import.meta.glob('../content/blog/*.md', {
  eager: true,
  query: '?raw',
  import: 'default',
})

export function getPosts(): PostMeta[] {
  return Object.entries(modules).map(([path, raw]) => {
    const frontmatter = parseFrontmatter(raw as string)
    return frontmatter as PostMeta
  }).sort((a, b) => new Date(b.date).getTime() - new Date(a.date).getTime())
}

function parseFrontmatter(raw: string): Record<string, string> {
  const match = raw.match(/^---\n([\s\S]*?)\n---/)
  if (!match) return {}
  return Object.fromEntries(
    match[1].split('\n')
      .filter(Boolean)
      .map(line => {
        const [key, ...rest] = line.split(':')
        return [key.trim(), rest.join(':').trim().replace(/^"|"$/g, '')]
      })
  )
}
```

Blog posts live in `src/content/blog/[slug].md` as Markdown files with YAML frontmatter. Clients add posts by creating new files in GitHub's web UI — no code required.

## File Structure

```
src/
  components/
    Navbar.tsx        — fixed, scroll-aware, hamburger
    Footer.tsx        — email capture strip + 3-column layout
    JsonLd.tsx        — JSON-LD structured data component
  sections/
    Hero.tsx          — text-dominant, no full-bleed photo overlay
    About.tsx         — Our Story + What We Value
    Services.tsx      — accordion numbered list
    Experiences.tsx   — visual showcase of specific bespoke experiences
    Testimonials.tsx  — editorial blockquote grid
    Blog.tsx          — featured + secondary posts, links to /[blog-path]
    Contact.tsx       — Formspree form + contact details
  pages/
    BlogIndex.tsx     — /[blog-path]
    BlogPost.tsx      — /[blog-path]/:slug
    NotFound.tsx      — 404 branded page with home link + primary CTA
  lib/
    blog.ts           — Markdown glob reader + frontmatter parser
  content/
    blog/
      [slug].md       — one file per blog post (Markdown + frontmatter)
  App.tsx             — BrowserRouter + Routes (including * → NotFound)
  index.css           — design tokens + blog typography
  main.tsx            — ReactDOM.createRoot
index.html            — SEO meta, Google Fonts (display=swap), JSON-LD
vercel.json           — SPA rewrite + security headers
public/
  robots.txt          — allow all, reference sitemap
  sitemap.xml         — static sitemap (update domain at handoff)
ITERATIONS.md         — iteration tracking
PITCH.md              — client-facing summary (generated at Phase 6)
MAINTENANCE.md        — client update guide (generated at Phase 6)
CONTENT.md            — extracted content + assets (generated at Phase 1)
.env.sample           — VITE_FORMSPREE_ID=your_formspree_form_id
```
