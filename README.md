# selenasolutions.com

Marketing site for Selena Solutions — a sole trader IT consultancy (ecommerce
builds, website refactoring, product engineering).

Static Astro site, one page, no client-side JavaScript. Deployed to Cloudflare
Pages.

## Develop

```sh
npm install
npm run dev          # http://localhost:4321, hot reload
```

Astro's dev server detaches into the background. Useful when it does:

```sh
npx astro dev status
npx astro dev stop
```

## Build

```sh
npm run build        # → dist/
npm run preview      # serve dist/ locally, exactly as deployed
```

## Deploy

```sh
npm run build
npx wrangler pages deploy dist --project-name selenasolutions --branch main
```

There is no CI. Deploys are manual on purpose — it's a one-page site and a
GitHub Action would be more moving parts than the thing it deploys. Add one when
that stops being true.

## Layout

Everything lives in `src/pages/index.astro`: content, markup and styles in one
file. The page is short enough that splitting it into components would cost more
than it saves.

```
public/
  _redirects       www → apex, 301
  robots.txt
src/pages/
  index.astro      the entire site
astro.config.mjs   site URL + sitemap
```

Content that changes often (services, process steps) is defined as arrays in the
frontmatter at the top of `index.astro` and rendered in a loop. Edit the arrays,
not the markup.

## Design notes

Light "soft UI" treatment: warm cream base, pastel grape/mint/bubblegum accents,
generous corner radii, soft shadows. Playful, but the claims stay concrete.

Two constraints worth preserving when editing:

- **Contrast is verified, not eyeballed.** The CTA colour (`--grape #6B3FF0` on
  white) is 5.84:1, body ink on cream is 15.76:1. An earlier revision used white
  on orange at 3.09:1, which failed WCAG AA on the most important element on the
  page. If you change a colour, compute the ratio.
- **The font is self-hosted** (`@fontsource-variable/plus-jakarta-sans`). No
  third-party font request, no layout shift on load. Keep it that way.

Also in place and easy to break by accident: skip link, 44px minimum touch
targets, visible focus rings, `scroll-margin-top` so anchors clear the sticky
header, and `prefers-reduced-motion` disabling all transitions.

## Infrastructure

| Thing | Value |
|---|---|
| Cloudflare Pages project | `selenasolutions` |
| Deploy preview | `selenasolutions.pages.dev` |
| Zone | `selenasolutions.com` (Cloudflare DNS) |
| Email | Cloudflare Email Routing, catch-all → personal inbox |

### DNS

The apex and `www` both need a **proxied CNAME** to `selenasolutions.pages.dev`.

Attaching a custom domain through the Cloudflare API registers it with Pages but
does **not** create the DNS record — the dashboard flow does both, which is why
this normally looks automatic. Via API the domain sits at `status=pending`
forever, waiting on a record nothing is going to write. Create the CNAMEs
yourself, or attach the domain from the dashboard instead.

Grey-cloud (unproxied) will not serve Pages and the certificate will not issue.

### Email

Cloudflare Email Routing forwards anything `@selenasolutions.com` to a personal
inbox. It is **receive-only** — replies go out from the personal address, not
from `hello@selenasolutions.com`. Sending as the domain needs an SMTP relay plus
Gmail "Send mail as", which is not set up.

Destination addresses require clicking a verification link before any rule can
reference them.

## License

Not open source. All rights reserved.
