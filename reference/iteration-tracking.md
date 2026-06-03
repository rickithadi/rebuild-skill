# Iteration Tracking Format

Create `ITERATIONS.md` at the project root at the start of every rebuild. Update it after every critique pass and every push.

## Template

```markdown
# [Site Name] — Rebuild Iterations

Source: [original URL]
Repo: https://github.com/[user]/[slug]

---

## Iteration N — [Short Description]
**Commit:** [git short hash]
**Branch:** main
**Vercel Preview:** [URL once auto-deployed, or "pending — check Vercel dashboard"]
**Critique Score:** N/40

**What was built / changed:**
- [bullet list]

**Critique findings fixed:**
- [P0/P1/P2 issues addressed]

**Mobile check:**
- 375px: [pass / issues found]
- 768px: [pass / issues found]

**Known remaining issues:**
- [anything not yet fixed — be honest]

---
```

## Rules

- Every `git push` gets an ITERATIONS.md entry before pushing
- Vercel URL recorded once available from dashboard — never run `vercel deploy` to get it
- Critique score recorded for every iteration that ran `/critique`
- Mobile check result recorded every iteration
- "Known remaining issues" is honest — don't omit issues to look cleaner
- The final iteration is marked `✓ FINAL` with handoff status

## Score Bands

| Score | Band |
|-------|------|
| 0–15 | Broken |
| 16–24 | Needs Work |
| 25–32 | Adequate |
| 33–36 | Good |
| 37–40 | Excellent |

Target: **36+** before Phase 6. If 5 iterations pass without reaching 36, exit the loop and document the gap.

## Iteration Cap

**Maximum 5 critique iterations.** If the score hasn't reached 36/40 by iteration 5:
1. Add a final entry marked `⚠️ MAX ITERATIONS REACHED`
2. Note the score achieved, the outstanding issues, and why they weren't resolved (missing client assets, structural limitation, etc.)
3. Proceed to Phase 6 — a 34/40 site with an honest handoff note is better than an infinite loop
