# Content Parity Checklist

Run this against `CONTENT.md` before declaring Phase 3 complete. Adapt to the detected site type — not every section applies to every site.

## Universal (all site types)

**Navigation**
- [ ] All nav link labels match source (exact text)
- [ ] All destination URLs correct, including external links (FAQ, booking, social)
- [ ] Mobile nav has the same links as desktop
- [ ] Primary CTA button label matches source (or is an editorially equivalent improvement)

**Hero**
- [ ] Headline uses source language or is a clear improvement of the same idea
- [ ] Subheadline / tagline editorially equivalent to source
- [ ] Primary and secondary CTA labels and URLs correct

**Testimonials** — zero tolerance for drift
- [ ] Every testimonial from the source is present
- [ ] Every quote is verbatim — character-for-character
- [ ] Every client name is spelled correctly
- [ ] Every context label (trip, project, location) is correct

**Contact Details** — verbatim
- [ ] Correct email address
- [ ] Correct phone number (if source has one)
- [ ] All social handles correct with proper platform URLs
- [ ] Booking/scheduling URL correct (TidyCal, Calendly, etc.)
- [ ] Physical address correct (if applicable)

**Credentials and Social Proof**
- [ ] Professional memberships verbatim (Virtuoso, bar association, etc.)
- [ ] Awards, press, and certifications present if on source
- [ ] Years in business / client counts / any claim numbers present if on source

**Footer**
- [ ] Navigation links present
- [ ] Legal links (Privacy Policy, Terms) present — link to source if not rebuilt
- [ ] Copyright year dynamic
- [ ] Social links correct

**SEO**
- [ ] `<title>` matches source title format
- [ ] `<meta name="description">` uses source tagline language
- [ ] `og:title` and `og:description` set
- [ ] `<link rel="canonical">` set
- [ ] `lang="en"` on `<html>`

---

## Service Business additions

- [ ] All service names verbatim
- [ ] Each service has a description and features list
- [ ] Founder name and exact credential string correct
- [ ] Key brand taglines verbatim ("Life's too short for mediocre vacations", etc.)
- [ ] Email capture list name and offer description match source
- [ ] Blog topics aligned to source categories

---

## Hospitality additions

- [ ] All rooms / menus / experiences represented
- [ ] Pricing shown if source shows it
- [ ] Opening hours correct
- [ ] Address and map present
- [ ] Reservation/booking flow links to correct destination

---

## SaaS / Product additions

- [ ] All pricing tiers present with correct names and prices
- [ ] Feature list accurate to source
- [ ] "Free trial" / sign-up CTA URLs correct
- [ ] FAQ covers same topics as source

---

## Portfolio additions

- [ ] All projects/case studies represented
- [ ] Client names correct on case studies (unless anonymised on source)
- [ ] "Available for hire" / contact CTA correct

---

## Professional Services additions

- [ ] All practice areas / service lines present
- [ ] Team names and titles verbatim
- [ ] Any regulatory disclaimers from source are included

---

## Image Placeholders

- [ ] Every `<img>` that needs client photography has a `data-replace` attribute
- [ ] Every placeholder is listed in `CONTENT.md § Image Placeholders`
- [ ] The PITCH.md photography section lists every placeholder
- [ ] No placeholder image is used in a way that misleads about the client's actual offering (e.g. don't use a stock photo of a blonde woman if the founder is a South Asian man)

---

## Out-of-scope pages

- [ ] Any pages not rebuilt are noted in `CONTENT.md § Multi-page Scope`
- [ ] Any pages not rebuilt are listed in the PITCH.md handoff
- [ ] Footer links to out-of-scope pages (FAQ, Terms, etc.) go to the original site URLs rather than 404ing
