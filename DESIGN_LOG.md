# Design Log — Design, Build, Ship, Assignment 1

Running log of design and technical decisions as we build. This is the raw
material for the final gallery "process" page (the assignment requires the
gallery to "tell the story of your process and present your final choice").
Entries are appended chronologically as decisions happen, not reconstructed
from memory at the end — so nothing gets lost or smoothed over in hindsight.

Each entry: what we decided, why, and what we rejected or ruled out.

---

## 2026-10-02 — Course context and CLAUDE.md setup

Read through the full Assignment 1 brief and the "Prototyping with Agents"
lecture transcript to understand the actual deliverable: 25 distinct landing
page versions + 1 gallery page telling the story, built in plain HTML/CSS/
optional vanilla JS only (no frameworks, no build step, no external APIs, no
data storage), deployed via GitHub + Vercel. Due **2026-10-06, 5:30 PM**.

Created `CLAUDE.md` at the `week1/` root capturing these constraints plus the
three working modes the lecture describes (Explore → Steer → Refine) so every
future session automatically has this context loaded.

**Decision:** No landing-page idea chosen yet at this point — deferred until
after a technique-exploration pass (see next entry).

## 2026-10-02 — Built `hw1/`: a 10-technique showcase, before picking the real idea

**Decision:** Before committing to a landing-page concept for the real 25-page
assignment, build a separate exploratory gallery (`hw1/`) proving out the most
visually/technically ambitious scroll and interaction techniques achievable
under the assignment's strict tech-stack constraint (no frameworks, no CDNs,
not even Google Fonts). Content/subject matter explicitly doesn't matter for
this pass — only visual and technical craft does.

**Why:** Wanted an honest read on the ceiling of plain HTML/CSS/vanilla JS
before locking in a direction, rather than discovering mid-build that a
technique isn't feasible under the constraints. Benchmarked against real
2026 trends via live web research (Awwwards reporting scroll-driven 3D/
spatial storytelling at 60fps now wins Site of the Day — immersive scroll
experiences went from 23% to 61% of winners year over year).

**The 10 techniques built**, each its own self-contained HTML file with zero
dependencies:
1. Scroll-scrubbed canvas icosahedron (3D projection math, no video)
2. Native CSS `animation-timeline: view()` kinetic typography — zero JS
3. Vertical-to-horizontal scroll hijack with 3-speed parallax
4. Raw hand-written WebGL fragment shader (no three.js)
5. SVG single-path point-interpolation morph across 4 shapes
6. Custom magnetic cursor with spring/lerp physics
7. CSS 3D perspective diorama (mouse-tilt + scroll-driven Z-depth)
8. Scramble/decode text reveal + native View Transitions API
9. Scroll-snap chapters with velocity-reactive canvas particles
10. 60-tile CSS grid assembly/explosion effect

**Rejected:** Using any JS animation library, CSS framework, Google Fonts, or
image assets — kept everything generative (canvas/SVG/CSS gradients) to stay
strictly within the "no external APIs" rule and prove the techniques are
honest, not propped up by a library doing the hard part.

## 2026-10-02 — Bug-hunting pass: visual verification beats console-error checking

Initial self-review only checked browser console for JS errors across all 10
pages — all clean. User pushed back ("i don't think a lot of them work as
intended — please check your work"), which was correct: **4 of 10 pages had
severe rendering bugs with zero console errors**, because the bugs were CSS
layout issues, not JS exceptions.

Root causes found and fixed, via real Playwright screenshots at 0%/33%/66%/
95% scroll depth on every page:

- **`overflow-x:hidden` on `<body>` breaks `position:sticky`** on any
  descendant (changes the sticky containing-block context) — affected pages
  03, 07, 10, causing their pinned scroll sections to render completely blank
  through the entire middle of the page. Fixed by removing the unneeded
  body-level overflow rule (the inner pinned containers already clip their
  own horizontal overflow).
- **`height:100%` on `<body>`** caps the whole document at exactly one
  viewport and makes it entirely unscrollable — affected page 04 (shader
  plasma), which never advanced past its first chapter no matter how far you
  scrolled. Removed.
- **`animation-timeline: scroll(root)`** without a scoped range animates
  across the *entire document's* scroll distance, not the local pinned
  section — affected page 02's "SCRUBBED BY SCROLL" de-rotation effect, which
  barely progressed within its own visible section. Fixed by giving the
  pinned wrapper its own named `view-timeline-name` and scoping the
  animation's range to `cover 0%` → `cover 100%` of that specific element.
- Also fixed earlier: unescaped apostrophes (`doesn't`, `isn't`) inside
  single-quoted JS string literals silently broke page 05's whole script.
- Minor polish: `index.html`'s fixed header used `mix-blend-mode:difference`,
  which produced illegible text-on-text collisions once content scrolled
  underneath it; replaced with a solid opaque background.

**Process lesson now baked into how we verify work going forward:** for any
scroll-driven or pinned page, "no console errors" is necessary but nowhere
near sufficient — verification must include actually viewing screenshots at
several scroll depths, since CSS/layout bugs render silently blank.

---

## 2026-10-05 — Real idea chosen: split 24 pages into two sites, reuse hw1 as the templates

**Decision:** The 25-page requirement is being split as: 12 versions of a
**personal site** for Hewitt, 12 versions of a **UChicago Club Lacrosse**
one-pager (lacrosse work starts later). `hw1/` is now the actual working
repo (git initialized, one commit already: "First intial commit. Playing
around with different website designs before deciding on concrete
direction") — the 10 technique-demo pages built earlier aren't being
discarded, they're being adapted directly into 10 of the 12 personal-site
versions. Committing after every round of changes from here on so the repo
history documents the iteration, per the assignment's grading requirement.

**Why:** The hw1 exploration already proved out 10 distinct, technically
strong directions — re-deriving 10 more from scratch for the personal site
would be wasted motion. Reusing them as a base, now populated with real
resume content, keeps velocity high while still hitting "radically different
directions," since each technique already reads nothing like the others.

**Content grounding:** Read the full resume (`personal-materials/
HewittWatkinsResume.pdf`) and all provided photos before any build work.
Wrote `personal-materials/content-brief.md` as the single source of truth
every build agent reads — avoids re-pasting the resume into 12 separate
prompts and keeps facts consistent across versions. Structural rule set for
every personal-site version: hero → Education → Work experience (each of the
3 roles gets its own distinctly styled section, not a bullet dump) →
Publication → Projects (each with an actual interactive visualization of the
algorithm, not just descriptive text) → Skills → Hobbies (golf, pickleball,
dogs) as the closing, lighter-tone section.

**Asset inventory confirmed from photos:** `main-headshot.jpg` (studio,
professional), `other-headshot.jpg` (casual outdoor) for hero/about;
`IMG_0102.JPG` is golf; three dog photos (`63908551552__...jpeg` small white
fluffy dog, `IMG_4638.jpeg` black doodle, `IMG_6640.JPG` white goldendoodle);
no pickleball photo exists — representing that hobby via text/icon/generative
CSS rather than a placeholder image. `IMG_9003.jpeg`, `DSCN3831.jpeg`,
`IMG_8988.jpeg` are lacrosse team photos — reserved for the lacrosse site,
explicitly excluded from the personal site.

**Template → personal-site mapping (10 adapted + 2 new, via 12 parallel
agents):**
| # | Base | Technique carried over |
|---|------|------------------------|
| 01 | `01-frame-scrub.html` | scroll-scrubbed 3D canvas geometry |
| 02 | `02-scroll-timeline-type.html` | native CSS `view()` kinetic type, zero JS |
| 03 | `03-horizontal-hijack.html` | horizontal scroll-hijack, 3-speed parallax |
| 04 | `04-shader-plasma.html` | raw WebGL fragment shader |
| 05 | `05-svg-morph-story.html` | SVG point-interpolation morph |
| 06 | `06-magnetic-cursor.html` | magnetic cursor + spring physics |
| 07 | `07-depth-diorama.html` | CSS 3D perspective diorama |
| 08 | `08-scramble-reveal.html` | scramble/decode text + View Transitions API |
| 09 | `09-particle-chapters.html` | scroll-snap chapters + velocity-reactive particles |
| 10 | `10-grid-assembly.html` | CSS grid tile assembly/explosion |
| 11 | *new* | Apple.com-inspired: bold type, dark hero, pill CTAs, product-style reveals |
| 12 | *new* | rishiravula.fyi-inspired: fully interactive fake terminal you type commands into |

**Checked feasibility before committing to #11/#12:** screenshotted both
reference sites live. Apple's DNA (huge sans-serif headlines, dark hero,
pill-shaped CTA buttons, alternating light/dark scroll sections) and Rishi's
site (a styled `<div>` acting as a terminal with a JS command parser —
`help`, `sumfetch`, etc., pixel/monospace font, no backend) are both fully
buildable in plain HTML/CSS/vanilla JS. No constraint violation on either, so
both get built.

**Execution:** launched 12 parallel fresh agents (no shared context with
each other or this session, by design — the lecture notes this keeps
divergent directions from bleeding into one another), each given: the full
content brief, its one specific template/direction to adapt, the exact
output path (`hw1/personal-v01.html` … `personal-v12.html`), and the known
bugs from the earlier QA pass to avoid reintroducing (`overflow-x:hidden`
breaking `position:sticky` for 03/07/10; `height:100%` making body
unscrollable for 04; unscoped `scroll(root)` barely animating for 02).

**Verification (before trusting any agent's self-report):** grepped all 12
output files for the known bug patterns first — 2 of 12 had reintroduced the
`overflow-x:hidden` + `position:sticky` bug despite being warned about it in
context (v02's pinned `.mega` text, v05's `.pin .viewport`); both fixed by
removing `overflow-x:hidden` from html/body. Then ran a full headless-browser
pass (console errors + screenshots at multiple scroll depths) across all 12
— zero console errors everywhere. One scare: v11's coarse screenshot
sampling landed on several blank-looking frames (missing feature cards,
missing project visualization); turned out to be a sampling artifact, not a
bug — the page has long, uneven section heights, and 6 evenly-spaced scroll
fractions across an 11,000px document kept landing on the *top* of a tall
section before its content lower down. Confirmed by scrolling every
`.feature-card` and `<canvas>` individually into view and checking computed
opacity/bounding box — all genuinely visible. Lesson: for pages with long,
unevenly-sized sections, verify specific elements directly rather than
trusting a fixed number of evenly-spaced scroll-fraction screenshots, which
can produce false-positive "looks blank" results purely from bad luck in
where the samples land. Also interactively tested v12's terminal (typed
`help`, `work capitalone`, `projects lzw`, `hobbies`, `contact`,
`nonsense123` via Playwright) — command parser, inline dog photos, and
unknown-command handling all work correctly.

**`personal-index.html` gallery built** linking all 12, following the same
dark-card-grid pattern as the technique-study `index.html`. All 12 links
verified resolving with no console errors.

## 2026-10-05 — Lacrosse 12-page site: research + brief, before launching the build

**Decision:** Build the second 12-page site now — a UChicago Men's Club
Lacrosse one-pager, 12 directions (`lax-v01.html`–`lax-v12.html`, same
`hw1/` directory, same naming convention as the personal site).

**Research done before writing content (facts grounded in real documents,
not invented):**
- `lax-materials/` turned out to contain more than photos: a "Print
  Submission.pdf" (the club's actual 2026-27 RSO re-registration form) and
  a "Club Lacrosse Constitution and Bylaws 2026-2027.pdf" — read both.
  Pulled the real officer roster (President Hewitt Watkins, Treasurer Lucas
  Lopez Forastier, Secretary Patrick Xia), league affiliation (GLLL,
  Division 2, **2024 D2 Champion at 11–0**), RSO email/address, and the
  official open-membership / non-discrimination language straight from
  these documents.
- **Deliberately excluded from the public-facing brief:** the full internal
  roster (18+ names) and everyone's personal `@uchicago.edu` email that
  appear in the RSO PDF — the user explicitly asked that confidential info
  not end up on the website, so `lax-materials/content-brief.md` only
  whitelists the 3 leadership names above (no emails/phones except
  Hewitt's, which he authorized) and instructs every build agent not to
  invent or publish anything from the full roster.
- `theglll.com` returned HTTP 403 to direct fetch, so used web search
  instead to ground opponents in reality: confirmed GLLL is a ~40-team
  Midwest club league (IL/IN/IA/MI/MN/ND/SD/WI) whose known programs
  include Notre Dame, Loyola Chicago, Carleton College, St. Norbert, and
  several Wisconsin schools (Whitewater/Platteville/La Crosse), plus a
  Madison, WI tournament site — used this as a "reasonable, not
  fabricated-from-nothing" opponent pool for an invented spring 2027
  schedule, per the user's explicit "make stuff up but keep it reasonable"
  instruction.
- Fetched the real `athletics.uchicago.edu/sports/womens-lacrosse` page
  (institutional maroon nav, scoreboard module, schedule/roster/news
  structure) specifically because the user asked for one of the 12 versions
  to mirror it.
- Read all 3 photos in `lax-materials/` closely (two championship-trophy
  shots — one on UChicago's Stagg Field, one at the Madison tournament
  site — and one full-team group photo) to describe them precisely for
  every build agent rather than leaving image usage to guesswork.

**Wrote `lax-materials/content-brief.md`** as the single source of truth
for all 12 build agents (mirrors the personal-site brief's role): team
identity, safe-to-publish contact/leadership info, the real **Nov 1, 2026
GLLL league meeting** date, photo descriptions, required page sections, the
same hard tech constraints and known-bug list as the personal site, and a
12-direction table.

**The 12 directions chosen** — explicitly picked to be distinct from each
other *and* from all 10 `hw1` technique demos *and* all 12 `personal-vNN`
directions already built (no reused frame-scrub/canvas-3D, CSS `view()`
kinetic type, horizontal-hijack, WebGL shader, SVG morph, magnetic cursor,
CSS diorama, scramble/view-transitions, particle chapters, grid assembly,
Apple-style, or terminal): broadcast scoreboard, varsity-institutional
mirror (the explicitly requested real-site homage), locker-room/tactile,
tactical whiteboard/field-diagram, recruitment poster, trophy-case museum,
vintage newsprint box-score, stadium jumbotron + region map, trading-card
roster, Scandinavian athletic-brand minimal, game-day hype reel, and
long-form season-preview editorial. Full table in the content brief.

**Execution:** launching 12 parallel Opus subagents (no shared context
between them, same reasoning as the personal-site build), each given the
full content brief, its one specific direction, and the exact output path.

**Rejected:** publishing the full team roster or anyone's email/phone
besides the president's (already public-by-consent) — not a design
decision so much as a hard content boundary the user set explicitly.

## 2026-10-05 — Lacrosse 12-page build complete; a real content-brief bug found and fixed mid-flight

**Bug found: the content brief's three photo descriptions were rotated one
position off from what the files actually show** (an error in how I wrote
the brief, not in the photos themselves). Caught because 10 of the 12
build agents independently opened the raw images, noticed the mismatch
against the brief, and self-corrected before writing their page — this is
exactly why each agent is told to verify assets itself rather than trust
prose blindly. Fixed the brief's photo section as soon as the first report
came in (`IMG_8988.jpeg` = the #10/#20 Stagg Field trophy shot, `IMG_9003.jpeg`
= the #10/#17 Madison trophy shot, `DSCN3831.jpeg` = the full-team group
photo — the brief originally had these three descriptions shifted by one).
2 of the 12 pages (v03, v08) had already picked up the stale mapping before
the fix landed; both were corrected in a follow-up commit.

**All 12 `lax-v01.html`–`lax-v12.html` now exist and passed an automated
audit:** grepped for the known-bug patterns (`overflow-x:hidden` on html/
body, `height:100%` on body, stale `../lax-materials/` paths, stray
`@uchicago.edu` emails beyond Hewitt's whitelisted one) across all 12 —
clean. Ran every inline `<script>` block through `new Function()` as a
syntax check (catches the single-quoted-apostrophe bug class from the hw1
QA pass) — all 12 parse cleanly. Each agent additionally ran its own
Playwright/headless-Chrome screenshot pass at desktop + mobile widths
through its scroll-driven sections before reporting done.

**Vercel-deploy fix (caught by the user, not by me — "remember this is
going to be deployed on Vercel"):** every page on both sites was still
reaching outside `hw1/` via `../lax-materials/...` and
`../personal-materials/...`, which only works locally because the parent
folder happens to be present — it breaks entirely once `hw1/` is the
deployed repo root, since nothing outside it ships to Vercel/GitHub. A
concurrent session running in parallel on this same repo caught this first
and landed "Bring all referenced image assets inside hw1/ for Vercel
deployment" — copying only the exact images each page references into new
`hw1/personal-materials/` and `hw1/lax-materials/` folders (deliberately
**not** the lacrosse RSO's internal Constitution/Bylaws or re-registration
PDFs, which contain the full roster's personal emails and must never ship)
and repointing every path from `../<folder>/` to `<folder>/`. Also cleaned
up ~20 stray debug screenshot files (`d_*.png`, `m_*.png`, a `shots/` dir)
that had leaked into `hw1/` root from agents' own QA screenshotting —
deleted before committing so they don't bloat the deployed repo.

**Concurrency note:** a second, independent Claude Code session was
working in this exact same `hw1/` repo at the same time (confirmed via
git log showing commits I didn't make — the Vercel-assets fix above, an
`index.html` gallery restyle, a `personal-v11` fix). Deliberately left
`personal-v*.html` and `index.html` untouched and uncommitted-by-me to
avoid stepping on that session's in-progress work; only committed the
lacrosse-specific files I was responsible for.

**Rejected:** publishing the full team roster or anyone's email/phone
besides the president's (already public-by-consent) — not a design
decision so much as a hard content boundary the user set explicitly. Every
agent confirmed this held on its own page.

## 2026-10-05 — v11 polish, then the same two upgrades across the other 11 personal pages

**v11 fixes (direct edits, not delegated):** user flagged three things on
the Apple-inspired page: (1) remove all orange, stick to black/white/gray
only — swapped the `--accent` orange for a neutral gray system, made the
primary CTA a solid white pill, removed every literal orange hex/rgba in
both CSS and the canvas JS; (2) the hero headshot was cropping out the
face — the source photo is a very tall portrait with the face in the
upper third, so default `object-position:center` on a 1:1 circle crop
landed on the shoulders. Enlarged 168px→248px and set
`object-position:50% 18%`; (3) added a persistent top banner (Index link
+ section nav, right-aligned) that's visible only at the very top of the
page and slides away on scroll — this turned into a site-wide pattern,
see below.

**Mid-turn, a new ask landed:** make the satellite-anomaly-detection
visualization "much cooler and higher quality" across every page,
grounded in the real project — it was trained on actual ESA (European
Space Agency) spacecraft telemetry archives (power, thermal, attitude,
communications channels) to detect real onboard malfunctions. Designed a
3-panel pattern as the v11 flagship, driven by one shared drift/time
function per page so all three stay in sync:
1. **Onboard Telemetry Channels** — 5-row strip chart, reaction-wheel
   speed is the fault channel that visibly drifts.
2. **Learned Latent Space (VAE)** — nominal-manifold scatter, dashed
   trained boundary, current point escapes it.
3. **Reconstruction Error vs. Threshold** — error climbs past a dashed
   alert line, triggers a blinking anomaly label.

**Also mid-turn:** user flagged that every page referenced images via
`../personal-materials/...` / `../lax-materials/...`, reaching outside
`hw1/` — fine locally, fatal once `hw1/` is the deployed Vercel root since
nothing outside it ships. Copied the exact referenced images into new
`hw1/personal-materials/` and `hw1/lax-materials/` folders and repointed
every path across all 24 version files. Verified by serving `hw1/` itself
as the HTTP root (exactly how Vercel serves it) — confirms this wasn't
just a cosmetic fix, it was the difference between a working and a
completely broken deployment.

**Execution:** launched 11 parallel fresh agents (v01–v10 + v12 terminal;
v11 already done directly) with a shared brief: rebuild the satellite
project as the 3-panel sim restyled to that page's own visual language,
and add the persistent top banner. Page-specific notes included each
page's known failure mode from earlier QA history (e.g. "don't add
overflow-x:hidden, this page uses position:sticky" for 03/07/10) so agents
wouldn't blindly reintroduce bugs already found once. Two pages got
different treatment by design: v05 keeps its signature SVG line-morph as
the hero and gets the 3 panels as compact supporting elements rather than
a replacement (the morph is the best-fitting visualization for this
project on the whole site); v12 (terminal) has no scrolling page to
attach a banner to, so it only got the visualization upgrade, rendered
inline via the `projects telemetry` command.

**Verification:** grepped all 11 for the two known bug patterns first
(clean — no reintroductions this round). Then real-browser checks on
every page: console-clean; banner show/hide confirmed by directly
manipulating `scrollTop` on each page's actual scroll container (not just
`window` — v09 uses an inner `#scroller`, confirmed its listener is
attached there, not window); every satellite visualization's anomaly
state confirmed to actually trigger via screenshots taken after waiting
through the full drift cycle, not just checking the DOM structured
correctly. One near-miss during verification: an automated heuristic for
"find the scrollable element" grabbed the wrong element for v09 and made
it look like the banner wasn't hiding — direct testing against the known
`#scroller` id showed it works correctly. Lesson: generic
scrollable-element detection is unreliable on pages with nested scroll
contexts; target the known id directly when one exists.

**Concurrency note:** while this was in flight, the other session
committed its own lacrosse-gallery work and, because it used a broad
`git add`, inadvertently swept up and committed this session's staged
`personal-v01–10,12.html` changes too (commit `871128e`, whose message
only mentions the lacrosse gallery). Confirmed via diff that nothing was
lost — the ESA/banner content is genuinely in that commit — just
misattributed in the message. No corrective history rewrite was done
(rewriting shared commit history is too risky without coordinating with
the other session first); this log entry is the correction instead.

## Open / next

- Both 12-page sites (24 versions total) exist, are linked from
  `index.html`, and are Vercel-deploy-ready (all assets live inside
  `hw1/`, verified by serving `hw1/` itself as the HTTP root).
- A GitHub remote is now configured
  (`github.com/hewittw/designbuildship-week1`); local `main` is 1 commit
  ahead of `origin/main` — not yet pushed.
## 2026-10-05 — Deployed; recovered a dropped commit first

Before deploying, found that the other session's own commit had been
rewritten (`871128e` → `ca7d6f3`) partway through — and the rewrite
dropped this session's satellite-viz/banner edits to `personal-v01–10,12`
from history. They survived only because they were still sitting
uncommitted in the working tree (`git status` showed them as modified
against a clean `HEAD`). Committed them explicitly under their own commit
(`affc24d`) rather than assuming the earlier commit still held them —
lesson: after any concurrent-session commit activity, always diff against
HEAD before trusting that your own work already landed.

Pushed `affc24d` to `origin/main`, then redeployed to Vercel production
(`vercel --prod`, already linked to `prj_lKyyFjGAfuSQYXakG1j735jJDhfo`)
since GitHub auto-deploy isn't connected yet. Confirmed live via direct
curl: `index.html`, `personal-v01.html` (contains the recovered ESA
content), and `lax-v01.html` all return 200 with no deployment protection
blocking access.

**Live:** https://designbuildship-week1.vercel.app
**Repo:** https://github.com/hewittw/designbuildship-week1

## Open / next

- First version is deployed and publicly reachable. All 24 versions +
  index.html gallery confirmed live.
- GitHub auto-deploy-on-push is still not connected (`vercel git connect`
  previously failed — needs the user's Vercel account linked to GitHub in
  the dashboard first). Until then, any future change needs a manual
  `vercel --prod` from `hw1/` after pushing.
- Worth a final full-site visual pass across both halves together before
  final submission, time permitting.

## 2026-10-05 — More real lacrosse photos added, woven in per-page

User added `lax-materials/2024 Season /` — 27 real high-res (~8MB each)
game/sideline photos from the club's actual 2024 season. Reviewed ~11 of
them directly to pick a representative set: action shots, candid
portraits, a celebration moment, and a second trophy photo. Resized 8 to
web-appropriate size (max 1600px long edge, JPEG q75 via `sips` — ~68MB of
originals down to ~4.5MB) and copied into `hw1/lax-materials/season2024/`
so they ship with the deploy. Documented each with a one-line description
in `lax-materials/content-brief.md` for the integration pass.

**User's explicit preference:** keep `DSCN3831.jpeg` (the full team-group
trophy shot) as the primary/hero photo everywhere — these 8 are
supplementary, not replacements.

Launched 12 parallel agents (one per `lax-vNN.html`) to each pick 2–5 of
the 8 photos that fit that page's own specific design conceit and weave
them in natively rather than a generic bolt-on gallery — e.g. new camera
tiles for the broadcast-scoreboard page's existing "multi-cam" grid, a
second row of lockers for the locker-room page, photos on each of the
three value plinths for the museum page, a second "insert set" of graded
trading cards, ribbon-board photo backdrops for the jumbotron page. Every
agent reused that page's own existing CSS/JS patterns (halftone engine,
card-tilt script, scroll-reveal observer, etc.) so no new JS was needed in
most cases.

Verified across all 12 before committing: grepped for the known bug
patterns (none reintroduced), confirmed every referenced `season2024-0N.jpg`
file exists on disk, loaded all 12 in a real headless browser checking
`naturalWidth` on every `<img>` (catches silently-broken image paths that
a console-error check would miss) *and* checking `::before`/`::after`
computed `background-image` (a plain `getComputedStyle(el)` call misses
pseudo-element backgrounds — v08 uses exactly that pattern for its ribbon
boards), then visually spot-checked 4 structurally-different pages with
real screenshots.

Committed in two pieces (photo assets first, then the 12 page edits) so
the assets were safely pushed even if something had gone wrong with the
later integration step. Pushed both and redeployed to Vercel production;
confirmed live via direct curl (`lax-v09.html` 200, a `season2024-06.jpg`
asset 200, and the expected photo references present in the served HTML).

## Open / next

- Both sites fully photo-complete and live.
- GitHub auto-deploy-on-push still not connected — future changes need a
  manual `vercel --prod` from `hw1/` after pushing.
- Git: no GitHub remote configured yet, and `gh` CLI isn't installed on
  this machine. User chose to keep committing locally for now rather than
  push immediately — still needs resolving before submission.
- Deadline: **2026-10-06, 5:30 PM** — one day out as of this log update.

## 2026-10-05 — Personal site expanded past 12: five real-app-UI clones (v13–v17)

User asked for five more personal-site versions beyond the original 12,
each cloning a real, recognizable app UI as the delivery vehicle for the
same resume content (same brief, same two headshots, same three dogs, same
three project algorithm visualizations): an Instagram clone, an iMessage
conversation, an Apple Notes clone, a Netflix browsing UI, and an original
"float while reading" all-white ocean-waves direction. Explicitly okay for
the personal section to exceed the 12/12 even split with the lacrosse
site — these are additive, not a replacement for any of v01–v12.

Launched 5 parallel Opus agents, one per direction, each pointed at the
same `personal-materials/content-brief.md` source of truth and the same
known-bug checklist as every prior build in this project. Results:

- **v13 — Instagram clone:** real grid→feed→story-viewer navigation,
  double-tap likes, swipeable carousels, tap-to-advance stories with a
  progress bar and poll slide.
- **v14 — iMessage thread:** resume delivered as a two-side conversation
  (visitor in blue, Hewitt in gray), typing indicators, tapbacks, read
  receipts, a functional quick-reply bar.
- **v15 — Notes app clone:** a genuinely working Apple Notes UI — sidebar
  note list is clickable and swaps the main pane, search/filter, tags,
  folders, checklists, dark mode, keyboard nav (↑/↓, `/`).
- **v16 — Netflix UI ("Hewittflix"):** profile-picker intro, hero banner,
  hover-to-preview title cards that play a mini version of each project's
  algorithm animation, full "More Info" modals.
- **v17 — "Adrift" (ocean waves):** the user's own brief verbatim —
  "fresh clean, white, ocean waves in the background, I want to feel like
  I'm floating reading it." Four-layer looping inline-SVG waves (transform/
  opacity only, no seam), scroll-linked depth tint from shallow to deep
  blue, gently bobbing frosted cards, system serif type.

All three project visualizations (satellite anomaly detection, live LZW
encoder, hash-join vs. nested-loop join) are real, independently
implemented per page — not reused as a single shared widget — in every
one of the 5 new versions, continuing the project's standing rule that
"interactive visualization" means the algorithm actually runs.

Each agent self-reported a pass against the 4 known-bug patterns
(`overflow-x:hidden` on html/body, `height:100%` on body, unscoped
`animation-timeline`, unescaped apostrophes in single-quoted JS strings).
Independently re-verified with a grep sweep across all 5 new files before
committing — clean.

**Bug found mid-build (unrelated to the new pages):** user deleted
`lax-materials/season2024/season2024-08.jpg` (a second Madison trophy
photo) from disk while it was still referenced on 4 of the 12 lacrosse
pages (v02, v06, v07, v11). Fixed by swapping each reference for a
different, real, currently-unused `season2024-0N.jpg` already on disk and
rewriting the adjacent alt text/caption to describe the new photo
accurately (none of them still claim to show a second trophy lift) —
verified with a full path-existence sweep across all 12 lacrosse pages
afterward, zero dangling references.

Wired all 5 new pages into `hw1/index.html`'s gallery (Part 1 grid grew
from 12 to 17 cards), corrected the hero page-count copy (24 → 29) and the
section count badge (12/12 → 17/17).

## Open / next

- All 29 pages now exist: 17 personal + 12 lacrosse. GitHub remote and
  Vercel production deploy were set up earlier the same day (superseding
  the stale note above) — public repo
  `https://github.com/hewittw/designbuildship-week1`, live at
  `https://designbuildship-week1.vercel.app`. GitHub→Vercel auto-deploy
  is still not connected (needs the user to add a GitHub login connection
  in the Vercel dashboard first); until then, publishing requires a manual
  `vercel --prod` from `hw1/` after each push.
- Next: commit and push v13–v17 + the gallery update + the lax photo fix,
  then redeploy to Vercel production.
- Deadline: **2026-10-06, 5:30 PM.**

## 2026-10-05 — v17 "Adrift" leveled up: depth-driven immersion rebuild

User feedback on the ocean-waves version: "the ocean one needs to be a
level up - i want it to feel incredibly modern and sophisticated and
that scrolling is immersive in the ocean." Diagnosis: the original build
only drifted from near-white to pale blue across the *entire* page —
too subtle to register as a descent.

Dispatched one Opus agent with a narrow, ambitious brief: keep the three
working project visualizations' underlying logic untouched, rebuild
everything about the atmosphere/depth system. Result (999 -> 1,447
lines): 8 real depth stages drive ~25 CSS color variables every frame
(sky, text, cards, chips, lines, nav, viz panels, glow), so the palette
genuinely travels white foam -> sunlit turquoise -> teal -> navy ->
near-black by the footer, with the light/dark text crossover timed to
land inside a single short stretch so nothing goes dark-on-dark. Added:
a real "going under" moment as the hero scrolls past, hand-rolled canvas
caustics (brightest in the shallows, fades out with depth), marine
snow/bioluminescent specks replacing sunlight in the deep, 3 parallax
bubble layers spanning the full scroll height, wavy zone-transition
dividers with a glass depth gauge (m / zone / atm), and two pointer
interactions (caustic ripple near the surface, glowing plankton sparks
in the deep).

Verified independently (not just trusting the agent's self-report): grep
sweep for all 4 known bug patterns (clean), a Node syntax check on the
inline script (passes), confirmed all 5 image paths still resolve, and
spot-checked that core facts (contact info, GPA, company names, the
exact LZW sample string) are still present verbatim. Agent also ran its
own headless-Chromium pass across ~20 scroll depths, mobile width, and
reduced-motion, and flagged one thing it *couldn't* verify: real-GPU
frame smoothness (headless Chromium has no GPU, so its slow-frame
numbers are unreliable) — worth a manual check in real Chrome/Safari if
it ever feels janky, especially Safari's backdrop-filter cost.

Also added a consistent fixed "← Index" back-link (top-right pill,
z-index above any modal) to v13-v17, matching the back-link every other
page in the site already had — a concurrent peer session doing an
unrelated 8-issue GitHub cleanup pass flagged that v13-v17 were the only
pages missing it.

**Working alongside a concurrent peer session (week1-41):** it was
independently fixing 8 open GitHub issues across lax-v01-12 and
personal-v12 at the same time as this upgrade. Both sessions share the
same local git working tree (not separate clones), so coordination was
by direct message rather than git: confirmed no file overlap up front,
each held its push until the other was ready, then both landed
cleanly — peer's 9 commits, then these 2 — with no merge conflicts since
the file sets never intersected.

## 2026-10-05 — Quizlet clone (v18), then seven more themed versions (v19–v25): hit 25 personal pages

Built `personal-v18.html` directly (not delegated): a genuine Quizlet
study-set clone — 7 real decks pulled from the resume (Education, the
three jobs, Projects, Skills, Hobbies), click-to-flip 3D cards, prev/next,
shuffle, a "still learning / got it" tracker with a completion banner, a
live-filtering search box over the deck library, and a "terms in this
set" list you can click to jump to any card. Verified headless: all 7
decks render, every interaction produces a real state change, zero
console errors.

User then asked for four more themes, then three more on top of that —
all seven handed to **parallel Opus subagents** in two batches of
4 + 3, each given the identical full resume content, the same known-bug
checklist, and one specific creative mandate, explicitly told not to
touch `index.html` or this log (to avoid collision while running
concurrently):

- **v19 — Five Nights at Freddy's:** a security-office night shift —
  door/light controls, a draining power meter tied to scroll depth, a
  camera map that jumps between every resume section as a labeled "feed,"
  CRT scanline/static overlay. Builds its own animatronic-silhouette
  iconography rather than using the real game's art.
- **v20 — New Zealand All Blacks:** true black/silver kit palette (no
  green), an expandable starting-lineup "team sheet" for the three jobs,
  a caps/stats scoreboard for skills, a full-bleed haka-style type beat.
- **v21 — Amazon:** a complete product-listing clone — buy box, cart,
  edition selector swapping the three jobs' bullets, star-rated reviews
  with filterable breakdown bars, "frequently bought together."
- **v22 — Black & white darkroom:** silver-gelatin monochrome, a headshot
  that visibly develops in over 4 seconds, an enlarger exposure slider,
  a light-table of negatives that flip to prints on hover; every photo
  forced through `grayscale(1)`.
- **v23 — Circuit board:** every section is a chip that visibly drops
  into its socket and powers on as you scroll, wired by real
  trace-routed signal pulses between modules; skills become a
  resistor/capacitor bill-of-materials.
- **v24 — Live binary:** every line of content is computed live from its
  real English source via `TextEncoder` (so the binary is always
  correct and reversible, not hand-typed) with a terminal-style decode
  button — and the "D" key — that turns it to English character-by-
  character, and back.
- **v25 — SpaceX Mission Control:** a sticky 360vh scroll-driven rocket
  launch — countdown, gravity turn, MECO, stage separation, second-stage
  ignition — with a live telemetry HUD (speed/altitude/G-load) reacting
  to scroll the whole way; the hero's own "payload" is the satellite
  telemetry project, so the theme is literally about the content it's
  carrying.

All three project visualizations (satellite anomaly detection, LZW
compression, SQL join) are real and independently implemented across
every one of the 7 — continuing the project's standing rule — and every
agent self-verified with a Node syntax check, a zero-tolerance headless
console/page-error pass, `naturalWidth` checks on every image, and by
actually exercising its own claimed interactions and confirming the
resulting state change, not just that the element exists. Two agents
caught and fixed real issues in their own work before reporting done
(one made a too-subtle drift animation visible after reviewing its own
screenshots; another found and fixed a ~200px scroll-position drift bug
in its own binary/English toggle).

**Email-link audit, prompted by user report ("it's not for me on a lot
right now"):** systematically checked every page for a real, clickable
`<a href="mailto:...">` rather than trusting the presence of the string
in the source. Found **9 of 18 personal pages** (v01, v02, v03, v04,
v07, v08, v09, v12, v18) had the email rendered as inert plain text, or
on the terminal page (v12), printed as plain output with no anchor at
all. All 12 lacrosse pages were already correct. Fixed all 9 directly
and re-verified in a real headless browser by locating the rendered
anchor element on each page post-fix, not by re-grepping the source.

**Mobile QA sweep caught two real, pre-existing layout bugs** neither
agent's own testing had hit: on `personal-v07.html`, the satellite
telemetry canvases (`flex:1` on a `<canvas width="600">`) and the
hobbies "dog-strip" photos (`flex:1` on `<img>`) were both missing
`min-width:0` — a flex item's default min-width is its content's
intrinsic size, so neither shrank below ~600px/aspect-ratio-width on a
390px viewport, overflowing the page horizontally. Root-caused by
hiding one overflowing element at a time and watching which one actually
moved `document.documentElement.scrollWidth`, rather than trusting
`getBoundingClientRect()` alone — several other elements that *looked*
off-screen (`.hl-glow`, `.hl-chips`, a sticky project card) turned out to
already be correctly clipped by an `overflow:hidden` ancestor and
weren't the real cause; a first attempt to "fix" them by adding
`overflow-x:hidden` to a non-sticky ancestor actually broke the page's
`position:sticky` hero instead of fixing anything, and was reverted
immediately once the sticky regression showed up in the same
verification pass. Also found the same nested-grid gotcha on the new
`brief-and-process.html` page itself: a `1fr` grid column was being
stretched wide by a nested `auto-fit, minmax(220px, 1fr)` grid's
intrinsic min-content size; fixed with `minmax(0, 1fr)` on the outer
column.

**Gallery restructured** to tell the explore→steer→refine story in its
own layout, not just describe it: the thirteen strongest personal
executions (the six app/brand clones plus all seven new themes, v13–v25)
are now featured at the top; the original twelve explore-phase bets
(v01–v12) are archived at the bottom under "Personal — First Attempt."
Added a gold "★ Final Pick" badge directly on the two chosen cards.

**New `brief-and-process.html` page** — the assignment's required
"tells the story of your process" gallery page, built as its own
long-form editorial piece with a sticky scroll-spy chapter nav: the
brief, the three phases above, and two "final choice" spotlights that
embed the actual live pages in real `<iframe>`s (not screenshots) next
to the reasoning for each pick:

- **Personal final pick: v25, "Mission Control."** Chosen over the
  other 6 new themes and the earlier app clones for being the most
  technically ambitious scroll choreography in the set, legible as
  "wow" within 3 seconds with no gimmick to discover first, and for
  being thematically self-referential (the launch theme's own payload
  is the satellite-telemetry project it's demonstrating).
- **Lacrosse final pick: lax-v10, "Scandinavian Minimal."** Chosen by
  actually screenshotting the four strongest candidates
  (broadcast-scoreboard, UChicago-Athletics homage, trophy-case museum,
  and this one) side by side rather than picking from gallery copy —
  wins on the brief's own refine-mode bar ("built to the level of a real
  product landing page") and on being the version most likely to
  actually convert a recruit, which is the page's real job.

A big "Read the Brief & Process →" CTA now sits in `index.html`'s hero,
and the stray debug screenshots (`edu.png`, `hero.png`, etc.) seven of
the subagents leaked into `hw1/` root during their own QA were deleted
before committing, per the standing rule from the first time this
happened.

Full-site final pass: every one of the 39 shipped files (37 content
pages + `index.html` + `brief-and-process.html`) re-verified together
at both 390px and 1440px — zero horizontal overflow, zero console/page
errors, anywhere.

## Open / next

- 37 total pages (25 personal + 12 lacrosse) + gallery + process page,
  all verified. Not yet committed, pushed, or redeployed as of this
  entry — next step.
- Deadline: **2026-10-06, 5:30 PM.**
