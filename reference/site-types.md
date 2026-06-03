# Site Type Templates

Detect the site type from CONTENT.md and use the matching section list and aesthetic direction below.

---

## Service Business
*Agency, consultancy, travel planner, wedding planner, interior designer, freelancer, coach*

**Detect by**: services page with custom packages, founder-led, testimonials prominent, booking/inquiry CTA

**Aesthetic direction**: Editorial Luxury or Warm Editorial depending on price point

**Section order**:
1. Navbar — fixed, primary CTA button
2. Hero — text-dominant, no full-bleed overlay, primary + secondary CTA ("Plan My Trip" / "Book a Call")
3. About — Our Story + values, founder bio, credentials
4. Services — accordion numbered list, progressive disclosure
5. Experiences — visual showcase of specific, differentiated deliverables (what makes them different)
6. Testimonials — editorial blockquote grid, verbatim, with context
7. Blog — only if source site has one. Featured + 2 secondary posts, links to blog index.
8. Contact — Formspree form + contact details
9. Footer — email capture strip + 3-column (Brand / Nav / Connect)

**Blog route**: match the source site's path exactly (`/blog`, `/journal`, `/news`, etc.). Never default to `/journal` unless the source uses that word.

---

## Hospitality
*Hotel, resort, restaurant, spa, vineyard, experience venue*

**Detect by**: room/table/experience booking, location is central to brand, photography-forward, seasonal offers

**Aesthetic direction**: Editorial Luxury (hotel/resort) or Warm Editorial (restaurant/café)

**Section order**:
1. Navbar — fixed, "Reserve" or "Book a Table" as primary CTA
2. Hero — full-bleed editorial photo with minimal type overlay IS acceptable here (it's expected by the audience). Use a single strong image, not a carousel.
3. About — brief brand story, what makes this place different, team/chef/owner
4. Offerings — rooms, menu sections, experiences — card or editorial list depending on count
5. Gallery — 4–6 images in editorial grid, no lightbox needed
6. Testimonials / Press — quotes + review platform logos if present
7. Location — embedded map + address + hours
8. Contact / Reservations — Formspree or link to booking platform
9. Footer — address + hours + social + legal

**Blog route**: optional — only add if source has one

---

## SaaS / Product
*Software, app, developer tool, API, platform*

**Detect by**: pricing page, feature comparison, "free trial" / "sign up" CTAs, technical copy

**Aesthetic direction**: Technical Precision or Bold Minimal

**Section order**:
1. Navbar — fixed, "Start Free" or "Get Started" as primary CTA, "Sign In" as ghost secondary
2. Hero — bold headline with specific value prop, product screenshot or demo video embed, primary CTA
3. Social proof strip — logos of notable customers or "X teams use [product]"
4. Problem / Solution — 2-column or numbered list explaining what pain this solves
5. Features — 3-column grid or alternating feature blocks (icon + title + description — keep icons small and purposeful)
6. Pricing — 2–3 tier cards, highlight recommended tier
7. Testimonials — 3 quotes with name, title, company logo
8. FAQ — accordion
9. CTA — final conversion block
10. Footer — 4-column (Product / Company / Resources / Legal)

**Blog**: only build if source site has one. Match the source route path.

---

## Portfolio
*Designer, photographer, illustrator, artist, filmmaker, architect*

**Detect by**: work/project gallery is primary content, single person, contact for hire CTA

**Aesthetic direction**: Expressive Maximalist (creative) or Bold Minimal (design/architecture)

**Section order**:
1. Navbar — minimal, name as logo, "Hire Me" or "Get in Touch"
2. Hero — name + title + brief positioning statement. No stock images — hero should be typographic or use their own work.
3. Work/Projects — masonry or editorial grid of case studies. Each project links to `/work/:slug` with full case study.
4. About — brief bio, skills, client types, download CV link if present
5. Services / What I Do — brief list if they offer defined services
6. Testimonials — 2–3 quotes from past clients
7. Contact — simple form or direct email link
8. Footer — minimal, social links

**Routes**: `/work`, `/work/:slug`

---

## Professional Services
*Law firm, accounting firm, medical practice, financial advisor, real estate agent*

**Detect by**: formal tone, credentials and certifications prominent, practice areas / specialisations, compliance-driven copy

**Aesthetic direction**: Bold Minimal or Technical Precision

**Section order**:
1. Navbar — fixed, "Schedule a Consultation" as primary CTA
2. Hero — clear statement of who they serve and what outcome they deliver. Authoritative tone.
3. Practice Areas / Services — clean list or simple cards, no accordion needed
4. Why Us — credentials, years in practice, notable results (without specifics that might be confidential)
5. Team — headshots + names + titles + brief bios
6. Testimonials / Results — quotes from clients, anonymised if needed
7. FAQ — accordion, field common questions that reduce cold inquiry overhead
8. Contact — form + office address + phone
9. Footer — address + phone + legal disclaimers + social

**Blog route**: `/insights`, `/insights/:slug` — important for authority and SEO

---

## E-commerce
*Product store, marketplace, subscription box, direct-to-consumer brand*

**Detect by**: product listings with prices, "Add to Cart", shopping cart, Shopify/WooCommerce indicators

**Note**: The standard React + Vite stack is not ideal for a real e-commerce build — this should be Shopify, Next.js + Shopify Storefront API, or similar. For a **pitch/sample** site, build a product-forward marketing site rather than a functional store.

**Aesthetic direction**: depends on product category. Fashion → Editorial Luxury. Consumer goods → Warm Editorial. Tech accessories → Bold Minimal.

**Section order (marketing site, not functional store)**:
1. Navbar — fixed, "Shop Now" as primary CTA linking to original store
2. Hero — product photography lead, clear value prop, primary CTA
3. Featured Products — 3–4 hero products with name, price, and "Shop Now" linking to original
4. Brand Story — why this product exists, founder story if present
5. How It Works / Why It's Different — features or differentiators
6. Social Proof — reviews, UGC mentions, press logos
7. Testimonials — product reviews verbatim
8. Email Capture — discount offer or launch list, prominent
9. Footer — links to original store + social + legal

---

## Aesthetic Directions Quick Reference

| Direction | Palette tendency | Font tendency | Use for |
|-----------|-----------------|---------------|---------|
| Editorial Luxury | Warm ivory + deep charcoal + earthy accent | Cormorant Garamond + Jost | Travel, hospitality, high-end services |
| Warm Editorial | Cream + warm brown + terracotta | Playfair Display + DM Sans | Food, wellness, lifestyle, boutique |
| Bold Minimal | Off-white + near-black + single electric accent | DM Serif Display + Inter | B2B, SaaS, agencies |
| Technical Precision | Cool white + charcoal + blue/teal accent | Libre Baskerville + Raleway | Dev tools, fintech, professional services |
| Expressive Maximalist | High contrast, multiple colours, unexpected combinations | Clash Display + Space Grotesk | Creative agencies, entertainment, fashion |

**Never default to navy + gold.** It is the AI luxury tell. Pick from the table above.
