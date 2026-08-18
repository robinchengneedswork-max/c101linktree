# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page link hub ("linktree") for Course 101 in Atlanta, run by Acts2 Network.
Course 101 is a 5-week course on the intellectual foundations of Christianity.
(It was seven weeks until the 10.07.2025 edition of the course book condensed it
to five — `c101-5wk_text_20251007.pdf`. Anything on the page still counting to
seven is a leftover, not a decision.)
Visitors arrive from a QR code, flyer, or social bio, and the page's only job is to
route them to one of two destinations:

1. The 2026 Course 101 sign-up Google Form (primary action)
2. `https://www.course101.online/` — the course content itself, in 8 languages

## Architecture

There is no framework, no build step, no dependencies. `index.html` is the entire
site: markup, `<style>` block, and an inline-SVG data-URI favicon in one file. Keep it
that way unless a second page is genuinely needed — the deploy story depends on it.

Design decisions worth preserving:

- **The masthead sets "C101" in Anton**, the same face the printed business cards use
  for that wordmark and the display face on rootedchurchatlanta.org. It links to
  course101.online. Its `clamp(1.6rem, 5vw, 2.1rem)` ceiling is deliberate: big enough
  to read as a mark, but the hero question must stay the first thing anyone reads. **Do
  not let the wordmark grow into the headline** — a page that opens with a question
  instead of a logo is the whole reason this doesn't look like every other link-in-bio.
- **The masthead reads `C101 • ATLANTA 2026`.** The year is the highest-value thing in
  the header: without it nobody scanning a card can tell a current page from a stale
  one. The org name is deliberately absent — the footer carries
  `rootedchurchatlanta.org · @rootedchurchatlanta`, so the header spends itself on what
  is unique to this page rather than repeating the host. If it needs to come back,
  `C101 • ROOTED CHURCH · ATLANTA 2026` still fits at 430px.
- **The divider is a saffron dot, not a rule.** It echoes the hero's `?` and is the one
  spot of brand colour in the lockup.
- **The hero is the course's first chapter title, "What is life?"** — not a logo or a
  generic welcome. The lede immediately explains that this is chapter one of five, so
  the headline and the chapter spine at the bottom are the same fact stated twice on
  purpose. Don't "fix" the repetition.
- **The two controls are not the same kind of thing.** "Sign up for 2026" is an `<a>`
  that leaves the page. "Explore the course" is a `<button>` that toggles the chapters in
  place — hence `button.link`, which resets UA button styling so it sits flush with its
  sibling card, and the chevron rather than the outbound arrow. The `.link:hover svg`
  rule is scoped `:not(.link__chevron)` so the arrow's nudge doesn't fight the chevron's
  rotation.
- **The disclosure starts open**, with `aria-expanded="true"` and no `hidden` attribute
  in the markup — five banners are short enough to show on arrival, and the JS only ever
  toggles from whatever the markup says, so the list also survives JavaScript being off.
- **The chapters live inside the links `<nav>`**, not in a section of their own, so they
  sit directly under the control that toggles them. There is no "five weeks, five
  chapters" heading — the disclosure's own label does that job.
- **Week number and destination chapter number do not line up, on purpose.**
  course101.online still hosts the seven-chapter edition, so the five weeks link to the
  nearest matching page: weeks 1–3 to `chapter-1/2/3`, week 4 to `chapter-4` (the book's
  "Our Predicament & God's Solution" covers the site's chapters 4 *and* 5), week 5 to
  `chapter-6` ("Our Response"), epilogue to `chapter-7`. Each banner shows the art
  belonging to the page it opens, which is why `ch5.webp` is currently unused — keep art
  and href together if you re-map these. If Acts2 republishes the site as five chapters,
  this whole mapping collapses back to 1:1.
- **The sixth row is the epilogue, and it is labelled rather than numbered.** "New Life
  of Love" closes the book after the five weeks; a sixth numeral would read as a sixth
  week, so it takes `.chapter__label` — the `.eyebrow` voice, wide-tracked caps in
  Instrument Sans — where the others take `.chapter__num`. It is the one row whose
  leading item is not in the serif, and it is deliberately smaller than the question
  beside it so it reads as a tag. Its question, "So what is life, after all?", re-asks
  the hero's on purpose: that is exactly what the epilogue's own first page does.
- **Each chapter banner** is 4:1, Acts2's illustration with a number and that week's
  question overlaid — nothing else. The official chapter titles ("A Good Thing Gone Bad"
  and so on) were deliberately dropped. Numbering is meaningful because the course is a
  progression; it isn't decoration. The full set runs green dawn → red → gold → night →
  desert → cliff → sunrise, tracking the course's own arc, so keep whichever are in use
  in that order.
- **Each chapter link carries an explicit `aria-label`** ("Week 1: What is life
  really about?"). Without it the accessible name comes out as `1What is life really
  about?` — the number and question are separate flex items, so they concatenate with no
  space. Keep the label in sync with the visible question if you change one.
- **`.chapter__text` carries a dark scrim, and its colours are literal, not tokens.**
  The illustrations range from near-white (ch5, ch6) to near-black (ch4), so
  overlaid text cannot trust the art underneath; the scrim is dark in both themes, which
  is why the text is `#FFFFFF` and the number `#F0B44A` rather than `var(--ink)` and
  `var(--saffron)`. Weakening the gradient breaks legibility on the pale chapters.
- **`.chapter__art` has both `aspect-ratio: 4/1` and `min-height: 6.25rem`.** The
  min-height is what stops the overlaid question from crowding once the column is narrow
  enough to wrap it onto a second line. Keep both if you change the ratio.
- **Every question on the page is set in Newsreader** — the hero, the five weekly
  questions, and the epilogue's. That is the rule: a question looks like a question. Chapter titles and
  labels stay in Instrument Sans. Don't set a new question in the sans.
- Colors and spacing come from CSS custom properties on `:root`, with a dark variant
  under `@media (prefers-color-scheme: dark)`. Change a color by editing the token, not
  the rule that uses it. Saffron (`--saffron`) is reserved for the hero "?", the chapter
  numbers, and focus rings — spending it elsewhere flattens the hierarchy.
- The page-load stagger uses `.rise:nth-child(n)` on the **direct children of `<main>`**.
  Adding, removing, or reordering a top-level block shifts every delay after it.
- `@media (prefers-reduced-motion: reduce)` kills all animation and transition. Any new
  motion must stay inside that guard.

## The sticky sign-up bar

`.stickybar` carries the CTA back into reach once the hero has scrolled away. With the
chapters open — which is now the default — the real sign-up button sits well above
someone who has just read the fifth question, the point at which they are most likely to
act.

- **`position: fixed`, not `sticky`.** A sticky element occupies flow and would also show
  at the top of the page. This must stay out of the way until the hero leaves.
- **It toggles `visibility`, not just `transform`.** Transform alone would leave the two
  links focusable while off-screen, so keyboard users would tab into an invisible bar.
- An `IntersectionObserver` on `.question` drives it. The reveal transition is cancelled
  by the global `prefers-reduced-motion` rule, which leaves it appearing instantly rather
  than sliding — that is the intended reduced-motion behaviour, not a bug.

**Debugging note:** IntersectionObserver callbacks are delivered during the rendering
step, which Chrome suspends for a backgrounded or occluded tab. Driving the page purely
through CDP will show the observer never firing and the bar never appearing, even though
both work. Force a paint (take a screenshot) before concluding it is broken.

## The QR code

The page generates a QR code for its own production URL, for whoever is making a
flyer. The encoder is written out in the `<script>` at the bottom of `index.html`
rather than pulled from a CDN, so the page keeps working behind networks that block
third-party hosts. It covers byte mode, EC level M, versions 1–10 — enough for any
URL up to 216 bytes.

- **`SHARE_URL` is deliberately not `location.href`.** Codes generated from a Vercel
  preview deployment or from localhost must still point at production. If the domain
  changes, that constant and the `.qr__url` text are the two places to update.
- The QR panel hard-codes white/`#101436` instead of using the theme tokens. Do not
  "fix" this to respect dark mode — scanners need the light-on-dark polarity that QR
  specifies, and an inverted code fails on many phone cameras.
- A 4-module quiet zone is painted into the canvas itself, so the downloaded PNG is
  valid standalone rather than relying on the page's white card for margin.

**If you touch the encoder, re-verify it.** Correctness here is not eyeballable — a
subtly wrong matrix still looks like a QR code. The check that caught a transposed
format-bit placement during development was a round-trip: render the matrix to raw
RGBA and decode it with the `jsqr` npm package, asserting the text comes back
unchanged. Note that comparing against another encoder's matrix is a *weaker* test —
implementations legitimately disagree on mask selection when penalty scores tie, and
any valid mask scans fine.

## Chapter images

`images/ch1.webp` … `ch7.webp` are Acts2's chapter illustrations for the *seven*-chapter
edition still on course101.online — the page currently uses 1, 2, 3, 4, 6, and 7. They were
pulled from the
Webflow CDN behind course101.online and self-hosted here so the page does not depend on
their bandwidth or file paths. **Reuse was confirmed with the user**, who is part of the
Acts2 network.

They were normalised on the way in: 1100×275 (4:1) `cover` crop, WebP q76, flattened
onto white because several had alpha. That took the set from 811KB to 75KB. Re-cut them
from the originals if the display ratio changes — do not rely on `object-fit` to reshape
an already-cropped banner, or you get a blind centre crop of a crop.

The originals are portrait-ish (0.95–1.67 aspect), so a 4:1 slice easily beheads the
subject. The crop uses `sharp.strategy.attention` **except for ch1 and ch4**, which are
pinned to `'centre'` — attention put ch1 on empty sky and lost its landscape entirely,
and dropped the figure out of ch4. If you re-cut these, look at the output; a bad crop
is not obvious from the file size alone. SVG sources need `{density: 400}` on input or
they rasterize soft.

## Palette provenance

The indigo (`--indigo #3B34A6`) and saffron (`--saffron #E39A1F`) were invented for this
page; they do not come from either parent brand. This was raised with the user, who
chose to keep them. Do not "correct" them toward a parent palette without asking.

For reference, the two parent brands and their real values:

- **Rooted Church** — `#00171F`, `#003459`, `#007EA7`, `#00A8E8`. Type: Anton + Epilogue.
- **Course 101 (Acts2)** — `#173E43` deep teal, `#679B88` sage, `#C1B6A9` warm sand.
  Signature device: wide-tracked uppercase section labels, which this page echoes in
  `.eyebrow` and `.spine__label`.

Note `#679B88` on white is about 2.6:1 — it fails WCAG for text despite Acts2 using it
that way on their primary button.

## Commands

Preview (no build):

```
node "$TMP/serve.js"          # any static server works; or:
npx serve .
```

Deploy to Vercel — zero configuration, framework preset *Other*, build and output
settings left blank:

```
npx vercel            # preview deployment
npx vercel --prod     # production
```

## Editing the links

Both destinations live in the `<nav class="links">` block. A link is three coupled
pieces — the `href`, the `.link__title`, and the `.link__meta` line. Update all three
together so the label still describes where the button goes.

## Facts that came from outside this repo

These were read off the live sites, not invented, and should be re-checked before
changing them: the "8 languages" claim comes from course101.online; the form is titled
"2026 Course 101 Sign-Up". Rooted's links are `rootedchurchatlanta.org` and
`instagram.com/rootedchurchatlanta`, both read off their own site. Note that
course101.online still describes Course 101 as a 7-week course; this page follows the
5-week book instead.

The per-week questions were drawn from the question list printed on the back of the
business card, one per week — but that card was printed for the seven-week course.
Weeks 1–3 keep their card questions; weeks 4 ("What has God done about it?"), 5 ("How
do I respond?") and the epilogue ("So what is life, after all?") were written here to
cover the book's condensed chapters 4 and 5, which absorb what were four separate weeks.
**Card and page are meant to match** — the card needs a reprint, and if its list
changes, change these too.

Do **not** point people to the contact form on course101.online. Rooted Church does not
run that site and cannot see what arrives there, so questions sent that way go
unanswered. A "questions before you sign up?" line was removed for this reason; if one
comes back it must use a Rooted-controlled address.

The local host is **Rooted Church** (rootedchurchatlanta.org), which describes itself as
"Part of Acts2 College, Acts2 Network, Send Network, & Southern Baptist Convention" and
serves students at Georgia Tech, Emory, UGA, and KSU. The page uses "Rooted Church"
because that is how the church writes its own name; the user referred to it as "Rooted
Christian Fellowship," so confirm which name this cohort should carry.
