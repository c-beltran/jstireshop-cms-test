# JS Tire Shop Fixed Flat — CMS proof of concept (DISPOSABLE)

> **This repo is a throwaway spike, not the live site.** See
> [`POC-NOTES.md`](POC-NOTES.md) first. The real site's repo and deployment
> are untouched by anything here.

Below is the original site README, copied as-is for reference (some details,
like the custom domain, don't apply to this POC copy).

---

Website for JS Tire Shop Fixed Flat, a tire shop in Hempstead, NY. Live at
https://jstireshop.com.

## Deployment

Hosted on GitHub Pages using the legacy Jekyll build, built from the `main`
branch at the repo root. **Every push to `main` goes live immediately** —
there is no staging environment and no build step to review first. Always
work on a branch and open a PR into `main`.

## Structure

- `_layouts/default.html` — the shared page shell (`<head>`, topbar, navbar,
  page content, call bar, footer, scripts) that every page renders through.
- `_includes/` — pieces shared across pages:
  - `head.html` — `<head>` contents: meta tags, canonical/OG tags,
    stylesheets, and the page's JSON-LD include chosen by the `jsonld`
    front-matter key (default `jsonld-business.html`).
  - `topbar.html` — the top strip with address, hours and phone.
  - `navbar.html` — the main site navigation.
  - `call-bar.html` — the mobile sticky "tap to call" bar.
  - `footer.html` — the site footer, including the required template credit.
  - `scripts.html` — JS library and script tags loaded before `</body>`.
  - `jsonld-business.html` / `jsonld-community.html` — structured data
    (`LocalBusiness` / community page), selected per-page via the `jsonld`
    front matter key.
- `_data/business.yml` — single source of truth for the shop's phone,
  address, email and social links, referenced as `site.data.business.*`. It
  drives the shared includes (`topbar.html`, `navbar.html`, `footer.html`,
  `call-bar.html`), both JSON-LD includes, and every phone number and `tel:`
  link in page bodies. Page titles/meta descriptions (front matter) and the
  hours text (in `topbar.html`/`footer.html`) are literal, not sourced from
  this file — grep for them when they change.
- Page files (`index.html`, `about.html`, `contact.html`, `community.html`)
  — each is Jekyll front matter (see below) followed by the page's body
  content, which is dropped into `_layouts/default.html`.
- `css/style.css` — site-specific styles (on top of `css/bootstrap.min.css`).
- `js/main.js` — site-specific JavaScript.
- `lib/` — vendored JS/CSS: `animate/animate.min.css`, `easing/easing.min.js`,
  `wow/wow.min.js`, and `owlcarousel/` (min JS/CSS), referenced from
  `head.html`/`scripts.html`. Bootstrap's CSS is vendored separately at
  `css/bootstrap.min.css`; jQuery, the Bootstrap JS bundle, Font Awesome,
  Bootstrap Icons and Google Fonts load from CDNs in the includes.
- `img/` — page images and the favicon.
- `assets/` — video files: `js-flat-fix-video.MP4` (shop video, muted/looped
  autoplay, ~1.8 MB) and `educational_video.mp4` (click-to-play via the
  native `<video controls>` UI, `preload="none"`, ~13 MB).
- `robots.txt` — static file, not templated.
- `/sitemap.xml` — not a file in this repo; generated at build time by the
  `jekyll-sitemap` plugin configured in `_config.yml`.
- `CNAME` — the custom domain (`jstireshop.com`) GitHub Pages serves the site
  under. Do not delete.

`css/style.css` and `js/main.js` are linked with a `?v=<build time>` query
string (set in `head.html`/`scripts.html`) so browsers fetch fresh files
after each deploy instead of a stale cached copy.

## Front matter keys

Each page's front matter (between the `---` fences at the top of the file)
sets:

- `title` — page `<title>` and `og:title`.
- `description` — meta description and `og:description` fallback.
- `og_description` — optional override for `og:description` (used by
  `community.html`; other pages fall back to `description`).
- `keywords` — meta keywords.
- `og_image` — path used for `og:image` (e.g. `/img/tire-shop.JPG`).
- `nav` — which navbar link gets the `active` class (`home`, `about`,
  `contact`); omit it for pages that aren't in the nav.
- `jsonld` — which `_includes/jsonld-*.html` partial to inject. Defaults to
  `jsonld-business.html` for all pages (set in `_config.yml`); overridden to
  `jsonld-community.html` on `community.html`.

## Preview locally

Jekyll only runs inside Docker — do not install Ruby, gems, or Homebrew/npm
packages on the host.

```bash
docker run --rm -v "$PWD":/srv/site -w /srv/site -e BUNDLE_PATH=/srv/site/vendor/bundle -p 4000:4000 ruby:3.3 bash -c "bundle install && bundle exec jekyll serve --host 0.0.0.0"
```

Then open http://localhost:4000.

## Gotchas

- Keep the "Designed By HTML Codex" / "Distributed By: ThemeWagon" footer
  credit and the HTML Codex license comment on every page — required by the
  template license.
- `img/tire-shop.JPG` must keep its uppercase `.JPG` extension. macOS is
  case-insensitive so this is easy to break locally without noticing, but
  GitHub Pages serves files case-sensitively — any reference must match the
  tracked filename's case exactly.
- `community.html` is intentionally left out of the navbar. It's still
  published and reachable directly by URL.
- Videos are kept lightweight: either `preload="none"` with click-to-play
  controls (like `educational_video.mp4`), or small, muted and looped if
  autoplaying (like `js-flat-fix-video.MP4`). Follow the same pattern for any
  new video.

## License

MIT — see `LICENSE`. Built on a template by HTML Codex, distributed by
ThemeWagon.
