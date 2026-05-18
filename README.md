# Paperix — marketing & legal site

Static site for [paperix.app](https://paperix.app) (when DNS is live).

For now, the assumption is that this gets hosted on **GitHub Pages from a
separate public repo** (because the Paperix code repo stays private, and
free-tier Pages requires a public repo).

## Files

```
index.html        Marketing landing page
privacy.html      Privacy policy — linked from the App Store listing
support.html      Support / FAQ — linked from the App Store listing
assets/
  style.css       Shared stylesheet (light + dark mode)
  app-icon-*.png  Icon at 1024/512/180/128/64/32 px
  apple-touch-icon.png
  favicon-32.png
  og-image.png    1200×630 social card
```

Everything is hand-written HTML + a single CSS file. No build step, no
JavaScript, no Jekyll, no Hugo. Works on any static host.

## First-time setup

The deploy script is hardcoded to push to
`git@github.com:abhijitbansal/paperix-site.git`. If that's you, skip to
step 3. Forks or other contributors: set `SITE_REMOTE=…` in the
environment, or write a different URL to `.site-remote` (gitignored).

1. **Create the public repo** on GitHub:
   ```bash
   gh repo create abhijitbansal/paperix-site --public --confirm
   ```
   No README, no license, no `.gitignore` from the template.

2. **Enable GitHub Pages** on the new repo:
   - Repo → Settings → Pages
   - Source: "Deploy from a branch"
   - Branch: `main`, folder: `/ (root)`
   - First publish takes ~1 minute and the URL appears on the same page

3. **First deploy:**
   ```bash
   ./scripts/deploy-site.sh
   ```

The published URL is `https://abhijitbansal.github.io/paperix-site/` —
that's the placeholder URL in App Store Connect's Privacy/Support
fields until a custom domain is pointed at it.

## Custom domain (later)

When you have `paperix.app` (or whatever) set up:

1. Add a `CNAME` file at the root of the public repo containing the domain
   (one line, no http://).
2. At your DNS provider, point the apex domain to GitHub Pages' IPs (or
   add a `CNAME` record for `www` → `<your-user>.github.io`).
3. Apple's review process re-checks the privacy/support URLs after a few
   days; update them in App Store Connect once DNS settles.

## Deploying changes

```bash
# Refresh icon + OG image from the current app icon generator. Idempotent;
# safe to run anytime. If pixels changed, commit the result.
./scripts/refresh-site-assets.sh

# Make any HTML/CSS edits in site/, commit them to the doc-scan repo as usual.
git add site/
git commit -m "site: tweak privacy policy wording"

# Push the site/ subtree to the public repo:
./scripts/deploy-site.sh
```

The release skill's stage 11 wires these together automatically on every
`release appstore` run, so you rarely need to invoke them by hand.

The deploy script uses `git subtree split` + `git push --force`. Force-push
is intentional — the public site repo is a derivative, not a primary
source of truth. All edit history lives in the (private) code repo.

## Editing without redeploying

Open any of the `.html` files directly in your browser (`file://`) — they
render correctly because the CSS path is relative. Useful for previewing
edits before pushing.
