# Course 101 — Atlanta

A one-page link hub for Course 101 (Acts2 Network), Atlanta. It routes visitors
arriving from a QR code, flyer, or Instagram bio to one of two places:

- **Sign up for 2026** → the Google Form
- **Explore the course** → the five weeks and the epilogue, shown in place (open by
  default), each linking to its page on
  [course101.online](https://www.course101.online/)

It also generates a QR code for its own address, so you can pull a print-ready PNG
for flyers without leaving the page.

## Preview locally

`index.html` is plain static HTML with no build step — open it directly in a
browser, or serve it if you prefer a real origin:

```
npx serve .
```

## Deploy

Vercel serves `index.html` from the repo root with zero configuration.

- **From the dashboard:** import the repo, leave Framework Preset as *Other*,
  and leave build/output settings blank.
- **From the CLI:** `npx vercel` to preview, `npx vercel --prod` to publish.

## Changing the links

Everything lives in the `<nav class="links">` block in `index.html`: the sign-up
link, the explore disclosure, and the six chapter links inside it. When changing
a destination, update the `href`, the `.link__title`, and the `.link__meta` line
together so the label still describes where the control goes. Chapter links also
carry an `aria-label` that must match their visible question.

If the site moves to a different domain, update `SHARE_URL` in the script at the
bottom of `index.html` and the `.qr__url` caption — that constant is what the QR
code encodes, and it is intentionally separate from the page's own address so
codes generated on a preview deployment still point at production.
