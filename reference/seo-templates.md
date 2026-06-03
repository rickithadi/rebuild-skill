# SEO Templates

## JSON-LD Component

Create `src/components/JsonLd.tsx` and render it in `App.tsx` (or the relevant page component):

```tsx
export default function JsonLd({ data }: { data: object }) {
  return (
    <script
      type="application/ld+json"
      dangerouslySetInnerHTML={{ __html: JSON.stringify(data) }}
    />
  )
}
```

Add to `index.html` `<head>` or render via the component — either works for static data.

---

## Schema by Site Type

### Service Business (agencies, travel planners, consultants, coaches)

```json
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "[Business Name]",
  "description": "[Brand tagline or mission statement from CONTENT.md]",
  "url": "https://[domain]",
  "email": "[email from CONTENT.md]",
  "telephone": "[phone if present]",
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "[city]",
    "addressCountry": "US"
  },
  "sameAs": [
    "[Instagram URL]",
    "[LinkedIn URL]"
  ],
  "hasOfferCatalog": {
    "@type": "OfferCatalog",
    "name": "Services",
    "itemListElement": [
      {
        "@type": "Offer",
        "itemOffered": {
          "@type": "Service",
          "name": "[Service Name 1]",
          "description": "[Service description]"
        }
      }
    ]
  },
  "founder": {
    "@type": "Person",
    "name": "[Founder Name]",
    "jobTitle": "[Title/credentials]"
  }
}
```

### Hospitality (hotels, restaurants, spas)

```json
{
  "@context": "https://schema.org",
  "@type": "Restaurant",
  "name": "[Name]",
  "description": "[Description]",
  "url": "https://[domain]",
  "telephone": "[phone]",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "[street]",
    "addressLocality": "[city]",
    "postalCode": "[zip]",
    "addressCountry": "US"
  },
  "openingHoursSpecification": [],
  "servesCuisine": "[cuisine type if restaurant]",
  "priceRange": "$$",
  "hasMap": "[Google Maps URL if available]"
}
```

Use `@type: "Hotel"` for hotels, `@type: "HealthAndBeautyBusiness"` for spas.

### SaaS / Product

```json
{
  "@context": "https://schema.org",
  "@type": "SoftwareApplication",
  "name": "[Product Name]",
  "description": "[Value proposition]",
  "url": "https://[domain]",
  "applicationCategory": "BusinessApplication",
  "operatingSystem": "Web",
  "offers": {
    "@type": "Offer",
    "price": "[starting price or 0 for free tier]",
    "priceCurrency": "USD"
  }
}
```

### Portfolio (designer, photographer)

```json
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "[Name]",
  "jobTitle": "[Title — e.g. Brand Designer]",
  "description": "[Bio from CONTENT.md]",
  "url": "https://[domain]",
  "email": "[email]",
  "sameAs": [
    "[LinkedIn]",
    "[Instagram]",
    "[Behance/Dribbble if present]"
  ]
}
```

### Professional Services (law, accounting, medical)

```json
{
  "@context": "https://schema.org",
  "@type": "ProfessionalService",
  "name": "[Firm Name]",
  "description": "[Description]",
  "url": "https://[domain]",
  "telephone": "[phone]",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "[street]",
    "addressLocality": "[city]",
    "postalCode": "[zip]",
    "addressCountry": "US"
  },
  "hasCredential": "[Bar admission, CPA, etc. if stated]"
}
```

---

## sitemap.xml template

Create `public/sitemap.xml`. Replace `[domain]` at handoff.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://[domain]/</loc>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://[domain]/[blog-path]</loc>
    <changefreq>weekly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

Add one `<url>` block for each route in the app. Blog post URLs are dynamic — note in MAINTENANCE.md that the client should update the sitemap when adding posts, or set up a dynamic sitemap generator.

---

## robots.txt template

Create `public/robots.txt`:

```
User-agent: *
Allow: /

Sitemap: https://[domain]/sitemap.xml
```

Replace `[domain]` at handoff.

---

## Open Graph image

The `og:image` in `index.html` should point to a real image. Preference order:
1. The `og:image` extracted from the source site in Phase 1
2. The hero image extracted from the source site
3. A branded placeholder at `/og-image.png` in `public/`

Note in PITCH.md if the og:image is a placeholder and the client should replace it.
