# danbratton.com — portfolio

Jekyll site hosted on GitHub Pages, served from the custom domain `danbratton.com`. Long-form PM/analytics case studies authored as markdown in `_projects/`.

## Local preview

**Requires Ruby 3.0 or newer.** macOS system Ruby (`/usr/bin/ruby`) is 2.6 and won't work — the current `github-pages` gem needs 3.0+.

Install a modern Ruby once:

```bash
brew install ruby
echo 'export PATH="/opt/homebrew/opt/ruby/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
ruby --version   # should report 3.x
```

Then from the repo:

```bash
cd ~/VSCodeProjects/portfolio
bundle install
bundle exec jekyll serve --livereload
```

Open <http://localhost:4000>. Saving a file auto-rebuilds.

> **Note:** You don't strictly need a local preview to deploy — GitHub Pages builds and serves the site on its own infrastructure. Local preview is only for iterating on styling and content before pushing.

## Adding a new case study

1. Create `_projects/<slug>.md` (e.g. `_projects/mural-image-panel.md`).
2. Add front matter:

   ```yaml
   ---
   title: "The full title of the case study"
   description: "One-sentence summary used in meta description and sharing previews."
   role: "Product Manager"
   company: "Company Name"
   timeline: "90 days, pre-launch"
   card_headline: "Short headline that appears on the project card"
   card_image: /assets/images/projects/<slug>.jpg
   order: 2
   skills: "comma-separated, list, of, skills"
   ---
   ```

   - `order` controls the grid sort on the home page (lowest first).
   - `card_image` can be `.jpg`, `.png`, or `.svg`. 16:9 (~1600×900) works best.
   - `card_headline` falls back to `title` if omitted.

3. Write the case study body in markdown. Use `##` for section headings — they render with the underline bar.

4. **For the TL;DR callout**, wrap it as:

   ```markdown
   <div class="tldr" markdown="1">

   ## TL;DR

   Body paragraphs here...

   </div>
   ```

   The `markdown="1"` attribute tells kramdown to parse markdown inside the div. Leave the blank lines around the inner content — they're required.

5. Blockquotes (`> quoted text`) render with the burnt-orange left border and quote-background styling.

## Replacing placeholder images

- `assets/images/projects/mural-image-panel.svg` — the project card image shown in the home-page grid. Replace with a real 1600×900 JPG/PNG and update the `card_image` path in `_projects/mural-image-panel.md`.
- `assets/images/favicon.svg` — simple "db" wordmark. Fine to keep or replace.

## Per-case-study OG images (dynamic, Liquid-rendered)

The social preview image for each page is generated dynamically at build time from a shared SVG template:

- **Template:** `_includes/og-image.svg` — the single source of truth for the OG design. Takes parameters `eyebrow`, `title`, `subtitle`, `tag`. Wraps long titles to two lines automatically.
- **Home page OG:** `assets/og/home.svg` — renders site-level info. Referenced from `index.md` via `og_image: /assets/og/home.svg`.
- **Per-project OG:** `assets/og/<slug>.svg` — looks up the project by slug, passes its title/company/timeline into the template. Each case study front matter points at its own file via `og_image: /assets/og/<slug>.svg`.

**When you add a new case study**, also create the matching OG file. The body is five lines:

```yaml
---
permalink: /assets/og/<slug>.svg
sitemap: false
---
{%- assign project = site.projects | where: "slug", "<slug>" | first -%}
{% include og-image.svg eyebrow=project.company title=project.title subtitle=project.timeline %}
```

Replace `<slug>` with the base filename of the case study (e.g. `mural-image-panel`). Add `og_image: /assets/og/<slug>.svg` to the case study's front matter.

### SVG-as-OG caveat

Major social platforms (LinkedIn, Twitter) don't always render SVG in `og:image` — they prefer PNG/JPEG. What the dynamic SVG pipeline gives you:

- Works when someone **opens the URL directly** in a browser.
- Works in **Slack** (which renders SVG previews).
- May not render in **LinkedIn or Twitter** previews, depending on crawler behavior.

If you care about guaranteed previews on a specific platform, you have two options:

1. **One-off screenshot:** open the SVG in a browser, export it to PNG at 1200×630, drop it next to the SVG, and point `og_image` at the PNG instead. Good for your most-shared case studies.
2. **GitHub Actions SVG→PNG conversion:** add a workflow that runs `rsvg-convert` or `sharp` on every push to rasterize the rendered SVGs. Keeps the dynamic pipeline but guarantees raster output. (Not included in this scaffold — add when you need it.)

## Deploying to GitHub Pages + danbratton.com

### First-time setup

1. Create a GitHub repo (any name; this scaffold uses `baseurl: ""` because the site lives at a custom apex domain — the repo name doesn't need to match).
2. `git init && git add . && git commit -m "Initial portfolio scaffold"`
3. `git remote add origin git@github.com:<username>/<repo>.git`
4. `git push -u origin main`
5. On GitHub: **Settings → Pages → Source** → `Deploy from a branch` → `main` / `(root)`.
6. Under **Custom domain**, `danbratton.com` should auto-fill from the committed `CNAME` file. If not, enter it manually.
7. Once DNS propagates, tick **Enforce HTTPS**.

### Porkbun DNS

Log in to Porkbun → Domain Management → Details on `danbratton.com` → DNS.

First, **delete any default parking records** (Porkbun adds ALIAS/A records on `@` when you register a domain — these will override the new records). Also clear the default `www` CNAME if one exists. Leave **URL Forwarding** empty.

Then add:

| Type  | Host    | Answer                    |
| ----- | ------- | ------------------------- |
| ALIAS | *(blank)* | `<username>.github.io`  |
| CNAME | `www`   | `<username>.github.io`    |

Replace `<username>` with your GitHub username. No trailing dots.

If the ALIAS record doesn't take for any reason, fall back to four A records at the apex (host blank):

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

### Verify

```bash
dig danbratton.com +short     # should return the GitHub Pages IPs
dig www.danbratton.com +short # should resolve via <username>.github.io
```

Then visit:

- <https://danbratton.com> — live site with valid TLS
- <https://www.danbratton.com> — 301 redirects to the apex

GitHub Pages provisions the Let's Encrypt certificate automatically (~15 minutes after DNS is live). You don't need to configure anything in Porkbun's SSL settings.

## Structure

```
portfolio/
├── _config.yml              # site config (title, author, url, collections)
├── CNAME                    # custom-domain marker for GitHub Pages
├── Gemfile                  # github-pages gem
├── _includes/
│   └── og-image.svg         # shared Liquid-templated OG image
├── _layouts/
│   ├── default.html         # <head>, favicon, OG/Twitter meta
│   ├── home.html            # hero, project grid, contact block
│   └── case-study.html      # title, .meta line, content, .skills footer
├── _projects/               # Jekyll collection — one .md per case study
│   └── mural-image-panel.md
├── assets/
│   ├── css/main.css         # Cowork-lifted styles + home-page additions
│   ├── og/                  # dynamically-rendered OG images (one per page)
│   │   ├── home.svg
│   │   └── mural-image-panel.svg
│   └── images/
│       ├── favicon.svg
│       └── projects/
│           └── mural-image-panel.svg  # card art for the home-page grid
├── index.md                 # home page content (layout: home)
└── README.md
```
