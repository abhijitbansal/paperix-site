# SnapDoc — marketing & legal site

Static site for [snapdoc.app](https://snapdoc.app) (when DNS is live).

For now, the assumption is that this gets hosted on **GitHub Pages from a
separate public repo** (because the SnapDoc code repo stays private, and
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

1. **Create an empty public repo** on GitHub. Suggested name: `snapdoc-site`.
   No README, no license, no `.gitignore` from the template (we'll push our
   own content).
   - Web UI: github.com/new → set Public → leave templates unchecked
   - or: `gh repo create <your-user>/snapdoc-site --public --confirm`

2. **Tell the deploy script where to push.** From the repo root:
   ```bash
   echo 'git@github.com:<your-user>/snapdoc-site.git' > .site-remote
   ```
   `.site-remote` is already in `.gitignore` so it won't end up in either
   repo. You can also pass `SITE_REMOTE=…` as an env var if you prefer.

3. **Enable GitHub Pages** on the new repo:
   - Repo → Settings → Pages
   - Source: "Deploy from a branch"
   - Branch: `main`, folder: `/ (root)`
   - First publish takes ~1 minute and the URL appears on the same page

The published URL will be `https://<your-user>.github.io/snapdoc-site/` —
that's the placeholder URL to put in App Store Connect's Privacy/Support
fields, until you point a custom domain at it.

## Custom domain (later)

When you have `snapdoc.app` (or whatever) set up:

1. Add a `CNAME` file at the root of the public repo containing the domain
   (one line, no http://).
2. At your DNS provider, point the apex domain to GitHub Pages' IPs (or
   add a `CNAME` record for `www` → `<your-user>.github.io`).
3. Apple's review process re-checks the privacy/support URLs after a few
   days; update them in App Store Connect once DNS settles.

## Deploying changes

```bash
# Make changes in site/, commit them to the doc-scan repo as usual.
git add site/
git commit -m "site: tweak privacy policy wording"

# Push the site/ subtree to the public repo:
./scripts/deploy-site.sh
```

The deploy script uses `git subtree split` + `git push --force`. Force-push
is intentional — the public site repo is a derivative, not a primary
source of truth. All edit history lives in the (private) code repo.

## Editing without redeploying

Open any of the `.html` files directly in your browser (`file://`) — they
render correctly because the CSS path is relative. Useful for previewing
edits before pushing.
