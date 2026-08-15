# Course 101 — Atlanta

A one-page link hub for Course 101 (Acts2 Network), Atlanta. It routes visitors
arriving from a QR code, flyer, or Instagram bio to one of two places:

- **Sign up for 2026** → the Google Form
- **Read the course** → [course101.online](https://www.course101.online/)

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

Both URLs live in the `<nav class="links">` block in `index.html`. Update the
`href`, the `.link__title`, and the `.link__meta` line together so the label
still describes where the button goes.
