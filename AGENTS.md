# Design, Build, Ship — Week 1 Agent Context

## Course
**MPCS 51238 · Autumn 2026 · UChicago**
Design, Build, Ship — Assignment 1: Accelerated Prototyping
**Due: Tuesday, October 6, at 5:30 PM**

## What We're Building
Two ideas, built in parallel, each as its own 12–25 page set behind one shared gallery (`index.html`):

- **Personal portfolio — Hewitt Watkins.** Page concept: a software-engineering resume/portfolio (work history, three real project demos, skills, hobbies). Who it's for: recruiters and engineers screening Hewitt for ML/backend roles. What a visitor should understand or do: grasp his experience and technical range fast, see real working project demos (not static descriptions), and leave with a way to reach him or pull his resume.
- **UChicago Men's Club Lacrosse — recruiting one-pager.** Page concept: a single-page recruiting site for a real RSO club team. Who it's for: prospective UChicago students deciding whether to join a club sport. What a visitor should understand or do: understand the team is real, competitive, and welcoming (no cuts, real 2024 GLLL Division 2 championship, 11–0), and sign up / show up to the next meeting.

25 personal versions and 12 lacrosse versions were built (37 pages total, exceeding the 25-page minimum), all linked from one gallery that is itself split into "Personal — Featured," "Lacrosse," and "Personal — First Attempt" (archived explore phase), plus a dedicated `brief-and-process.html` page telling the explore → steer → refine story and naming the two final picks.

## My Role
I am the user's agent for this assignment. My goal is to help them build at high velocity and at a level above what others in the class are doing. The class has ~28 students each making 25 designs (~700 websites total) — uniqueness is graded. I push for radically different directions and do not produce safe, generic work.

## Design Log (standing practice — not optional)
`DESIGN_LOG.md` at the project root is a running log of every real design and
technical decision: what we chose, why, and what we rejected. Append to it
as decisions happen, not retroactively at the end — this is the raw material
the user will use to generate the assignment's required gallery "process"
page (the gallery must "tell the story of your process and present your
final choice"). Log entries for: direction choices during explore/steer/
refine, anything rejected and why, real bugs found and fixed, and narrowing
decisions (what got dropped between v10→v19→v25).

## Assignment Requirements (Non-Negotiable)
- **25 distinct landing page versions** (v01–v25) in a single project
- **1 gallery page** (`index.html`) — thumbnails of all 25 versions, each linking to its page, each with a short note on what changed
- Each version: explore a radically different direction (layout, audience, mood, era, tone — NOT just colors/fonts)
- v01–v10: go wide (magazine spread? brutalist manifesto? calm single-column? dark academia? startup SaaS?)
- v11–v19: narrow down — mix what works (typography from v5 + layout from v11)
- v20–v25: converge and refine the winning direction
- Deploy to **Vercel** (free Hobby plan) from a **GitHub repo**
- Submit: Vercel URL + GitHub URL with commit history showing iteration

## Tech Stack (Hard Constraints)
- **Plain HTML + CSS + optional vanilla JS only**
- No frameworks (no React, Vue, Svelte, etc.)
- No build step, no npm, no package.json
- No external APIs, no data storage
- If I suggest a framework or package: push back

## File Structure
```
project-root/            (this is the Vercel deploy root and the GitHub repo root)
├── index.html                  ← gallery page (all personal + lacrosse versions)
├── brief-and-process.html      ← process narrative + final picks
├── personal-v01.html … v25.html
├── lax-v01.html … v12.html
├── personal-materials/         ← headshots, dog photos, resume PDF
└── lax-materials/               ← team photos
```

## How I Work With the User

### Explore Mode (v01–v10)
- Generate radically different directions without being asked to stay consistent
- Think: brutalist, editorial, Web1.0 retro, Japanese minimalist, Y2K, dark sci-fi, Notion-clean, hand-drawn, luxury fashion, gov.uk utility
- Each version is a completely different creative bet
- Save each as its own file — never overwrite

### Steer Mode (v10–v19)
- Identify what's working across versions
- Combine elements: layout from one + typography from another + palette from another
- Make micro-iterations visible and documented

### Refine Mode (v20–v25)
- Lock in the winning direction
- Get extremely precise: spacing, type scale, interactions, wording
- Build to the level of a real product landing page

### Gallery Page
- Automatically update `index.html` after each new version
- Each thumbnail: version number, short one-line description of what changed/tried
- Clicking any thumbnail opens that version
- Gallery itself should be impressive — not an afterthought

## Design Vocabulary (Use These Terms)
Hero, navbar, CTA, card, modal, toast, tooltip, banner, accordion, pill, badge, skeleton screen, segmented control, empty state, sticky header, sidebar, gutter, above the fold, type scale, leading, tracking, weight.

## What "Next Level" Looks Like
Things no one else in the class is doing:
- Interactive micro-animations with pure CSS
- Versions that target completely different eras (1994 web, 2007 skeuomorphic, 2013 flat, 2024 glassmorphism, etc.)
- Versions for radically different audiences (children, enterprise, luxury, accessibility-first)
- A gallery page that's itself a compelling design artifact
- Every commit message names the creative direction, not just "update v03"

## Key Principles (From the Lectures)
- "It looks finished but it's rarely complete" — always drill into the details
- Ask for 10 options, then remix what works from each
- Vague early, precise late
- Judge the page itself — not the agent's description of it
- Keep a trail: each version tells the story of arrival at the final design
- The user steers; the agent executes

## Useful Commands
- `/context` — verify this file loaded
- `/rewind` — undo last change
- Screenshot + paste → fastest way to give design feedback
