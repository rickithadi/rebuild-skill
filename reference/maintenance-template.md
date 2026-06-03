# MAINTENANCE.md Template

Generate this file at Phase 6 handoff. It goes to the client when they sign.

Write it in plain English. No code blocks unless absolutely necessary — and if you include one, explain every line. The audience is a business owner with no developer.

---

## Template

```markdown
# [Site Name] — How to Update Your Website

This guide covers everything you can update yourself, without needing a developer.

---

## Adding a Blog Post

Your blog is powered by simple text files that live in your GitHub repository. You don't need to know how to code.

1. Go to your GitHub repo: https://github.com/[user]/[slug]
2. Navigate to `src/content/blog/`
3. Click **Add file → Create new file**
4. Name it: `your-post-title.md` (use hyphens, no spaces)
5. Paste this template at the top, fill it in:

```
---
title: "Your Post Title"
date: "2026-06-15"
category: "Travel Tips"
excerpt: "A one-sentence summary that appears on the blog index."
slug: "your-post-title"
---

Your post content starts here. Write in plain paragraphs.

## Section Headings Use Two Hashes

- Bullet points work like this
- And like this
```

6. Click **Commit new file** at the bottom
7. Your post will appear on the site within 2–3 minutes automatically

---

## Updating Your Contact Details

1. Go to: `src/sections/Contact.tsx`
2. Find the line with your email address and update it
3. Find the line with your social handles and update them
4. Click **Commit changes**

Or, more simply — just email us and we'll update it in under 10 minutes.

---

## Updating Your Services

Your services are in `src/sections/Services.tsx`. Each service has:
- A `title`
- A `description`
- A `details` list of what's included

Find the service you want to change, update the text, commit.

---

## Replacing Placeholder Photos

Your site currently has [N] placeholder photos that should be replaced with your own. Each one is marked with a comment in the code explaining what the photo should show.

**The easiest way**: email us your photos (high-resolution JPGs or PNGs) with a note about which section each one is for. We'll update them.

**If you want to do it yourself**: the photos are in the sections listed below. Replace the `src="..."` URL with a link to your own hosted image (e.g. uploaded to your own CDN, Google Drive public link, or similar).

Photos to replace:
[List each item from CONTENT.md § Image Placeholders]

---

## Setting Up Your Contact Form

Your inquiry form is currently in demo mode. To activate it:

1. Go to [formspree.io](https://formspree.io) and create a free account
2. Click **New Form**
3. Set the email to: [client email address]
4. Copy the **Form ID** — it looks like `xrgvpkjq`
5. In your Vercel dashboard: [project] → Settings → Environment Variables
6. Add a variable: `VITE_FORMSPREE_ID` = [paste your Form ID]
7. Click **Redeploy** — forms will now arrive in your inbox

Free plan: 50 submissions/month. More than enough for most inquiry sites.

---

## Pointing Your Domain

If you have an existing domain and want to use it:

1. Go to your Vercel project dashboard
2. Click **Settings → Domains**
3. Click **Add Domain** and enter your domain
4. Vercel will show you DNS records to add — copy them
5. Go to your domain registrar (GoDaddy, Namecheap, etc.)
6. Add the DNS records Vercel gave you
7. Wait up to 24 hours — usually much faster

---

## What Needs a Developer

These things are possible but require someone comfortable with React and Vite:
- Adding a completely new section type
- Changing the site's color scheme or fonts
- Adding new pages beyond the existing routes
- Integrating a new third-party service
- Anything not covered above

Estimated time for most of these: 1–2 hours.

---

## Getting Help

For urgent issues or changes beyond this guide:
[Your agency contact details here]
```

---

## Notes for generating this file

- Replace every `[placeholder]` with actual values from CONTENT.md and the build
- The placeholder photo list comes directly from `CONTENT.md § Image Placeholders`
- The Formspree email is the client's email from `CONTENT.md § Contact`
- If the site has no blog, remove the blog section entirely
- If the site has no contact form, remove the Formspree section
- Keep the tone warm and practical — the client is reading this after signing, not before
