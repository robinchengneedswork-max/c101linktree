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

- **The hero is the course's first chapter title, "What is life?"** — not a logo or a
  generic welcome. The lede immediately explains that this is chapter one of seven, so
  the headline and the chapter spine at the bottom are the same fact stated twice on
  purpose. Don't "fix" the repetition.
- **The chapter spine** lists all seven real chapter titles. Numbering is meaningful
  here because the course is a progression; it isn't decoration.
- Colors and spacing come from CSS custom properties on `:root`, with a dark variant
  under `@media (prefers-color-scheme: dark)`. Change a color by editing the token, not
  the rule that uses it. Saffron (`--saffron`) is reserved for the hero "?", the chapter
  numbers, and focus rings — spending it elsewhere flattens the hierarchy.
- The page-load stagger uses `.rise:nth-child(n)` on the **direct children of `<main>`**.
  Adding, removing, or reordering a top-level block shifts every delay after it.
- `@media (prefers-reduced-motion: reduce)` kills all animation and transition. Any new
  motion must stay inside that guard.

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
changing them: the seven chapter titles and the "8 languages" claim come from
course101.online; the form is titled "2026 Course 101 Sign-Up". **"Atlanta" was inferred
from the folder name `c101_ATL`** and has not been confirmed against a source — verify
it before printing flyers.
