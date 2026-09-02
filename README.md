# crawfordsmetaldetectorsreviews.co.uk

Static review site for Crawfords Metal Detectors, generated from a single data
file and deployed on GitHub Pages.

## How it works

```
data/products.json   ← single source of truth (scores, stock, images, links)
build.py             ← generates every page as real static HTML (SEO-safe URLs)
sync.py              ← refreshes live stock from crawfordsmd.com
.github/workflows/   ← the weekly sync + the one-click "Run workflow" button
assets/              ← shared CSS + JS (mobile menu, tracking safety net)
```

Every "Buy at Crawfords" link carries Sam's affiliate tracking code
(`?tracking=682a88c59a`), baked in at build time, so all traffic and sales from
this site are recorded against the main site's affiliate account.

## The weekly sync ("sync click")

Two ways to run it:

1. **One click:** GitHub repo → **Actions** tab → *Weekly data sync & rebuild*
   → **Run workflow**. It fetches live stock for every product, rebuilds all
   pages, commits, and GitHub Pages redeploys automatically.
2. **Automatic:** the same workflow runs every Monday 06:00 UTC.

If crawfordsmd.com's firewall blocks the GitHub runner, the sync fails safe —
existing data is kept, the log lists which products couldn't be checked, and
the site still deploys. In that case run locally from an office machine:

```
python3 sync.py && python3 build.py
git add -A && git commit -m "manual sync" && git push
```

If the sync log shows a product flipped to **Out Of Stock**, decide whether it
stays on the leaderboard (temporary outage) or moves to `retired` in
products.json (end of line) — the Vanquish 540 Pro-Pack precedent.

## Editing content

- **Scores / blurbs / ranking:** edit `data/products.json`, run
  `python3 build.py`, commit. Ranking is fully editorial — the `rank` field is
  the order, full stop.
- **New review page:** add a page function in `build.py` (copy
  `page_manticore` as the template — it carries the full schema stack), set the
  product's `review_status` to `live` and its `review_slug`.
- **Retiring a product:** move its entry to `retired` in products.json and
  rebuild — it disappears from every page in one step.

## Local preview

Pages use root-absolute paths, so open via a local server, not file://

```
python3 -m http.server 8000     # then http://localhost:8000
```

## GitHub Pages setup (one-off, for Sam)

1. Repo → Settings → Pages → Source: `main` branch, `/ (root)`.
2. The `CNAME` file already points at crawfordsmetaldetectorsreviews.co.uk.
3. DNS at GoDaddy: four A records on `@` (185.199.108.153 / .109 / .110 /
   .111) + `www` CNAME to `<github-username>.github.io`.
4. Tick **Enforce HTTPS** once the certificate is issued.

## Launch checklist

- [ ] Replace `image.png` catalogue shots with original testing photos where possible (E-E-A-T)
- [ ] Add Trustpilot TrustBox + Facebook Page Plugin embeds on /customer-reviews/
- [ ] Add GA4 tag + UTM parameters alongside the affiliate code
- [ ] Submit sitemap.xml in Google Search Console
- [ ] Link to this site from crawfordsmd.com (footer or blog)
- [ ] Publish remaining reviews per the launch calendar before promoting those URLs
