# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page link hub ("linktree") for Course 101 in Atlanta, run by Acts2 Network.
Course 101 is a 7-week course on the intellectual foundations of Christianity.
Visitors arrive from a QR code, flyer, or social bio, and the page's only job is to
route them to one of two destinations:

1. The 2026 Course 101 sign-up Google Form (primary action)
2. `https://www.course101.online/` — the course content itself, in 8 languages

## Architecture

There is no framework, no build step, no dependencies. `index.html` is the entire
site: markup, `<style>` block, and an inline-SVG data-URI favicon in one file. Keep it
that way unless a second page is genuinely needed — the deploy story depends on it.

Design decisions worth preserving:

- **The masthead sets "C101" in Anton**, which is the same face the printed business
  cards use for that wordmark and the display face on rootedchurchatlanta.org. It is a
  logo lockup, so keep it small — the hero question is the thing meant to be read first.
- **The hero is the course's first chapter title, "What is life?"** — not a logo or a
  generic welcome. The lede immediately explains that this is chapter one of seven, so
  the headline and the chapter spine at the bottom are the same fact stated twice on
  purpose. Don't "fix" the repetition.
- **The two controls are not the same kind of thing.** "Sign up for 2026" is an `<a>`
  that leaves the page. "Explore the course" is a `<button>` that opens the chapters in
  place — hence `button.link`, which resets UA button styling so it sits flush with its
  sibling card, and the chevron rather than the outbound arrow. The `.link:hover svg`
  rule is scoped `:not(.link__chevron)` so the arrow's nudge doesn't fight the chevron's
  rotation.
- **The chapters live inside the links `<nav>`**, not in a section of their own, so they
  open directly under the control that reveals them. Each is a link to
  `course101.online/chapter-N/en` (the `/en` suffix is the English variant; the site has
  eight). There is no "seven weeks, seven chapters" heading — the disclosure's own label
  does that job.
- **Each chapter banner** is 4:1, Acts2's illustration with a number and that week's
  question overlaid — nothing else. The official chapter titles ("A Good Thing Gone Bad"
  and so on) were deliberately dropped. Numbering is meaningful because the course is a
  progression; it isn't decoration. The images run green dawn → red → gold → night →
  desert → cliff → sunrise, tracking the course's own arc, so keep them in order.
- **Each chapter link carries an explicit `aria-label`** ("Chapter 1: What is life
  really about?"). Without it the accessible name comes out as `1What is life really
  about?` — the number and question are separate flex items, so they concatenate with no
  space. Keep the label in sync with the visible question if you change one.
- **`.chapter__text` carries a dark scrim, and its colours are literal, not tokens.**
  The seven illustrations range from near-white (ch5, ch6) to near-black (ch4), so
  overlaid text cannot trust the art underneath; the scrim is dark in both themes, which
  is why the text is `#FFFFFF` and the number `#F0B44A` rather than `var(--ink)` and
  `var(--saffron)`. Weakening the gradient breaks legibility on the pale chapters.
- **`.chapter__art` has both `aspect-ratio: 4/1` and `min-height: 6.25rem`.** The
  min-height is what stops the overlaid question from crowding once the column is narrow
  enough to wrap it onto a second line. Keep both if you change the ratio.
- **Every question on the page is set in Newsreader** — the hero and all seven chapter
  questions. That is the rule: a question looks like a question. Chapter titles and
  labels stay in Instrument Sans. Don't set a new question in the sans.
- Colors and spacing come from CSS custom properties on `:root`, with a dark variant
  under `@media (prefers-color-scheme: dark)`. Change a color by editing the token, not
  the rule that uses it. Saffron (`--saffron`) is reserved for the hero "?", the chapter
  numbers, and focus rings — spending it elsewhere flattens the hierarchy.
- The page-load stagger uses `.rise:nth-child(n)` on the **direct children of `<main>`**.
  Adding, removing, or reordering a top-level block shifts every delay after it.
- `@media (prefers-reduced-motion: reduce)` kills all animation and transition. Any new
  motion must stay inside that guard.

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

`images/ch1.webp` … `ch7.webp` are Acts2's chapter illustrations, pulled from the
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
`instagram.com/rootedchurchatlanta`, both read off their own site.

The seven per-chapter questions are drawn from the question list printed on the back of
the business card, one per week. **Card and page are meant to match** — if the card's
list changes, change these too, and vice versa.

Do **not** point people to the contact form on course101.online. Rooted Church does not
run that site and cannot see what arrives there, so questions sent that way go
unanswered. A "questions before you sign up?" line was removed for this reason; if one
comes back it must use a Rooted-controlled address.

The local host is **Rooted Church** (rootedchurchatlanta.org), which describes itself as
"Part of Acts2 College, Acts2 Network, Send Network, & Southern Baptist Convention" and
serves students at Georgia Tech, Emory, UGA, and KSU. The page uses "Rooted Church"
because that is how the church writes its own name; the user referred to it as "Rooted
Christian Fellowship," so confirm which name this cohort should carry.
