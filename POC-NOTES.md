# jstireshop-cms-test — throwaway proof of concept

**This whole repository is disposable.** It is a copy of the live
`jg-flat-fix` site created only to prove that a non-technical shop owner
could log in to a CMS **without a GitHub account**, swap homepage photos and
a video link, and have photos automatically resized at build time — with no
work happening in, or risk to, the live site's repo.

Nothing here is production code. It is not linked from the live site, it is
not the live site's git history, and it should not be built on top of —
either start a fresh implementation informed by what worked here, or copy
specific pieces (the `_data/home.yml` pattern, the resize step in
`.github/workflows/pages.yml`) into the real repo deliberately.

## What's in here

- A full copy of the `jg-flat-fix` Jekyll site (same layouts/includes/CSS/JS),
  re-pointed at `https://c-beltran.github.io/jstireshop-cms-test` so it is a
  self-consistent, independently deployable site.
- `_data/home.yml` — the two homepage sections a shop owner can edit: the
  `promo` section (an image, or a click-to-play YouTube embed) and the
  4-photo `gallery` row. `index.html` reads both via Liquid.
- `admin/` — Decap CMS 3.x, loaded from a CDN, editing `_data/home.yml`
  through a friendly (Spanish-language) form. See "DecapBridge setup" below —
  **login does not work yet**, on purpose (see Constraints in the task this
  came from: no accounts were created).
- `.github/workflows/pages.yml` — builds with `actions/jekyll-build-pages`
  and deploys to GitHub Pages. Before the build, it resizes/compresses
  anything in `img/uploads/` (where Decap CMS drops uploaded images) to a
  1920px long edge / ~75% JPEG quality, so a huge phone photo never ships
  full-size. This step only touches the runner's working copy — it never
  commits anything back to the repo.
- `robots.txt` is set to `Disallow: /` and there is no `CNAME`, so this
  should not get indexed or attached to the real domain.

## DecapBridge setup — what a human must do next

This POC intentionally stops short of a working login, per the task
constraints ("do not sign up for anything, do not create accounts"). A human
needs to:

1. **Sign up** at <https://decapbridge.com> (free tier is enough for a POC).
2. **Create a "Site"** in the DecapBridge dashboard and link it to the
   `c-beltran/jstireshop-cms-test` GitHub repo (this is also where
   DecapBridge will ask to install its GitHub App/token access — that's the
   "git gateway" it uses to commit CMS edits on the owner's behalf).
3. DecapBridge will show a **site id**. Take that value and paste it into:
   - **File:** `admin/config.yml`
   - **Line:** the `identity_url` line inside the `backend:` block:
     ```yaml
     identity_url: https://auth.decapbridge.com/sites/DECAPBRIDGE_SITE_ID_PLACEHOLDER
     ```
     Replace `DECAPBRIDGE_SITE_ID_PLACEHOLDER` with the real site id (leave
     everything else in that URL as-is).
4. In the DecapBridge dashboard, **invite the shop owner's email** as a
   collaborator on the site. They'll get an email to set a password (or use
   "Login with Google"/"Login with Microsoft" if PKCE mode is enabled) — no
   GitHub account needed.
5. Commit and push the updated `admin/config.yml`, then visit
   `https://c-beltran.github.io/jstireshop-cms-test/admin/` and log in.

No other placeholder values are needed — `repo:` and `branch:` in the same
`backend:` block are already filled in with this repo's real values, since
those don't depend on signing up for anything.

## Spanish UI

Decap CMS supports it: the admin loads the full `decap-cms.js` bundle (not
the smaller `decap-cms-app` bundle), which registers every bundled locale
pack automatically, so `admin/config.yml` only needs one line:

```yaml
locale: "es"
```

Field labels and hints inside the `home` collection in `admin/config.yml`
were also written in Spanish directly, since Decap CMS only localizes its
own chrome (buttons, dialogs, error messages) — not collection/field labels,
which are always author-supplied text.

## How to delete everything

1. Delete the GitHub repo: `gh repo delete c-beltran/jstireshop-cms-test --yes`
   (this also tears down its GitHub Pages site).
2. If a DecapBridge account/site was created for this POC, delete the "Site"
   (and the account, if it was created solely for this test) from the
   DecapBridge dashboard.
3. Delete the local clone: this repo's working copy on disk.

No other systems were touched — the live `jg-flat-fix` repo, its GitHub
Pages settings, and its DNS/CNAME were never modified.
