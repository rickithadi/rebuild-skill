# PITCH.md Template

Generate this file at Phase 6 handoff. Write it for the client, not for a developer.
Plain English. No technical jargon. The goal is to make the client feel excited and clear on next steps.

Fill in every `[bracketed placeholder]` from CONTENT.md and ITERATIONS.md.

---

## Template

```markdown
# [Site Name] — Your New Website

Hi [Founder name / team],

We put together a modern rebuild of your website. Here's what we made and what it means for you.

---

## What We Built

A new version of [Site Name] using the same content you have today — your services, your testimonials, your story — designed to feel more premium, work better on mobile, and convert visitors into inquiries more effectively.

**Live preview**: [GitHub repo URL or Vercel URL]

---

## What's Different

| Before | After |
|--------|-------|
| [describe a visible weakness of the source site — e.g. "text-heavy hero with a generic stock photo overlay"] | [what replaced it — e.g. "editorial headline with clean typography that leads immediately to your CTAs"] |
| [second weakness] | [what replaced it] |
| [third weakness] | [what replaced it] |

(3 rows is enough — pick the most visible improvements)

---

## What We Kept

Everything that matters:
- All your service descriptions
- Every testimonial, word for word
- Your credentials and professional memberships
- Your contact details and booking link
- Your brand voice and key phrases

---

## What's Ready

- [x] Full site with [N] sections
- [x] Blog with [N] articles on [topics]
- [x] Contact form (needs one quick setup step — see below)
- [x] Mobile-optimised layout
- [x] SEO meta tags with your brand language

---

## Two Things Needed to Go Live

### 1. Photography
The site currently uses placeholder images. Your own photos will make a significant difference to how the site feels. We need:

[List each image placeholder from CONTENT.md § Image Placeholders, one per line]
- Hero image: [description of what's needed]
- About section: [description]
- [etc.]

### 2. Contact Form
The inquiry form needs a 2-minute setup:
1. Go to [formspree.io](https://formspree.io) and create a free account
2. Create a new form pointed at [client email address]
3. Copy the Form ID (looks like `xrgvpkjq`)
4. Send us the ID and we'll connect it in under 5 minutes

Once those two things are done, the site is ready to launch.

---

## Timeline

We can have this live on your domain within [X] days of receiving the photography and Formspree ID.

---

Any questions, just reply to this email.

[Your name / agency name]
```

---

## Notes for generating this file

- **Before/After table**: pull the 3 most visible improvements from ITERATIONS.md critique findings. Frame them from the client's perspective ("your hero felt generic" → not "the hero used a full-bleed Unsplash overlay").
- **Photography list**: copy directly from `CONTENT.md § Image Placeholders`. If the client has custom photography already visible on the source site, note that those photos can be provided by them.
- **Tone**: warm and confident. This is a pitch, not a spec sheet. Avoid words like "scaffold", "component", "framer motion", "TypeScript".
- **Timeline**: leave the `[X] days` for the person sending to fill in — don't invent a delivery date.
- **Length**: the whole document should be readable in 2 minutes. Cut anything that doesn't help the client decide "yes, I want this."
