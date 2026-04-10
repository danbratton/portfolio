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

Two placeholder assets are SVGs that you should eventually replace with real art:

- `assets/images/og-default.svg` — the default social-sharing preview. LinkedIn, Twitter, and Slack all prefer PNG or JPEG here. Export a 1200×630 PNG (`og-default.png`) and then change the default path in `_layouts/default.html` from `og-default.svg` to `og-default.png`.
- `assets/images/projects/mural-image-panel.svg` — the project card image for the Mural case study. Replace with a real 1600×900 JPG/PNG and update the `card_image` path in `_projects/mural-image-panel.md`.

The favicon (`assets/images/favicon.svg`) is a simple "db" wordmark — fine to keep or replace.

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
├── _layouts/
│   ├── default.html         # <head>, favicon, OG/Twitter meta
│   ├── home.html            # hero, project grid, contact block
│   └── case-study.html      # title, .meta line, content, .skills footer
├── _projects/               # Jekyll collection — one .md per case study
│   └── mural-image-panel.md
├── assets/
│   ├── css/main.css         # Cowork-lifted styles + home-page additions
│   └── images/
│       ├── favicon.svg
│       ├── og-default.svg   # replace with 1200x630 PNG for real social previews
│       └── projects/
│           └── mural-image-panel.svg  # replace with real 16:9 artwork
├── index.md                 # home page content (layout: home)
└── README.md
```
