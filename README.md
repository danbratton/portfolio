# danbratton.com — portfolio

Static portfolio hosted on GitHub Pages at `danbratton.com`. Long-form PM/analytics case studies authored as plain HTML in `projects/`. Home page uses Bootstrap 5 (via CDN) for the card grid; case study pages use a custom editorial stylesheet with no framework.

**Why Jekyll (lightly):** every page reuses the same shared `<head>` block — favicon, OG meta, canonical URL, etc. Jekyll's `{% include %}` lets you define it once in `_includes/head.html` and drive every meta tag (description, `og:description`, `twitter:description`, etc.) from a single front-matter variable on each page. No drift when you reword a description.

## Local preview

You need Ruby 3+. macOS ships Ruby 2.6, which is too old.

### One-time setup

```bash
brew install ruby
echo 'export PATH="/opt/homebrew/opt/ruby/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
ruby -v        # should report 3.x or 4.x

cd ~/VSCodeProjects/portfolio
bundle config set --local path 'vendor/bundle'
bundle install
```

(If you're on Intel Mac, the Homebrew path is `/usr/local/opt/ruby/bin` instead of `/opt/homebrew/opt/ruby/bin`.)

### Run the dev server

```bash
bundle exec jekyll serve --livereload
```

Open <http://localhost:4000>. Saving a file rebuilds in ~1s and the browser reloads automatically.

### Why not the `github-pages` gem?

GitHub's official `github-pages` gem pins Jekyll 3.9, which uses `String#tainted?` — a method Ruby removed in 3.2+. So the official "matches the server exactly" gem is incompatible with any modern Ruby. Rather than pinning to ancient Ruby, this repo uses Jekyll 4.x directly and deploys through a custom GitHub Actions workflow (`.github/workflows/pages.yml`) that runs the same Jekyll 4.x version on CI. Local and remote builds stay in lockstep.

## Adding a new case study

1. Copy `_templates/case-study.md` → `projects/<slug>.md`. (The `_templates/` directory starts with an underscore, so Jekyll excludes it from the build — the template never ends up on the live site.)

2. Edit the front matter block at the top of the new file. The template has placeholder values for all of these:

   ```yaml
   ---
   layout: case-study
   title: "Full case study title"
   description: "One sentence used for meta description, og:description, twitter:description."
   role: "Your role"
   company: "Company name"
   timeline: "Time window or constraint"
   og_type: article
   permalink: /projects/<slug>.html
   ---
   ```

   The `description` field is the single source of truth — it populates all four description meta tags via `_includes/head.html`. Editing it once is enough. `role` / `company` / `timeline` feed the `.meta` line under the `<h1>` via the `case-study` layout.

3. Replace the body with your case study content, authored in plain markdown:
   - `##` for section headings (kramdown renders them as styled `<h2>` with an underline bar and auto-generated anchor ids for deep links)
   - `**bold**`, `*italic*`, `` `code` ``, `> blockquote` all work as expected
   - `- ` for bulleted lists, `1. ` for numbered lists
   - Curly quotes, em-dashes, and ellipses are produced automatically by kramdown's smartypants

   **Two places where you drop in a styled HTML wrapper** (kramdown needs `markdown="1"` to parse markdown inside them):

   ```markdown
   <div class="tldr" markdown="1">
   ## TL;DR

   Your TL;DR paragraphs in markdown.
   </div>
   ```

   ```markdown
   <div class="skills" markdown="1">
   **Skills demonstrated:** comma-separated list.
   </div>
   ```

   The `<h1>` (page title), `.meta` line (role/company/timeline), and back-link to `/` are emitted automatically by the `case-study` layout — you don't write them in the body.

4. Add a card on the home page. Open `index.html` and duplicate the existing `.project-card` block inside `.project-grid`:

   ```html
   <div class="col-12 col-md-6 col-lg-4">
     <a href="/projects/<slug>.html" class="card project-card h-100 text-decoration-none">
       <div class="card-image">
         <img src="/assets/images/<slug>.jpg" alt="Alt text">
       </div>
       <div class="card-body">
         <p class="card-eyebrow">Company · Role</p>
         <h2 class="card-headline">Short card headline</h2>
       </div>
     </a>
   </div>
   ```

   Drop the card artwork in `assets/images/<slug>.jpg`. 16:9 (~1600×900) works best.

## Styling cheatsheet

The editorial palette is defined in `assets/css/main.css` as CSS variables:

```css
--text:   #1a1a1a;
--muted:  #5a5a5a;
--accent: #c2410c;   /* burnt orange */
--rule:   #e5e5e5;
--bg:     #fafaf8;   /* warm off-white */
```

Body type is Georgia serif; headings and UI chrome are system-sans. The home page opts into Bootstrap 5 via `bootstrap: true` in its front matter, which makes `_includes/head.html` emit the Bootstrap CDN link. Case study pages omit that flag and ship Bootstrap-free. `main.css` always loads *after* Bootstrap, so editorial rules win on any class collisions.

## Layout & includes

- **`_layouts/default.html`** — the HTML skeleton (doctype, `<html>`, `<head>`, `<body>`). Every page uses this via `layout: default`.
- **`_includes/head.html`** — the shared `<head>` contents. Reads `page.title`, `page.description`, `page.og_type`, and `page.bootstrap` from each page's front matter; falls back to `site.title` / `site.description` from `_config.yml` when a page doesn't set its own.

## Deploying to GitHub Pages + danbratton.com

### First-time setup

1. Create a GitHub repo (any name; this site lives at a custom apex domain, so the repo name doesn't need to match).
2. `git init && git add . && git commit -m "Initial portfolio scaffold"`
3. `git remote add origin git@github.com:<username>/<repo>.git`
4. `git push -u origin main`
5. On GitHub: **Settings → Pages → Source** → **GitHub Actions**. (This is the newer deploy mode — it runs `.github/workflows/pages.yml` on every push to `main`. Do not pick "Deploy from a branch".)
6. Under **Custom domain**, `danbratton.com` should auto-fill from the committed `CNAME` file. If not, enter it manually.
7. Once DNS propagates, tick **Enforce HTTPS**.

The Actions workflow in `.github/workflows/pages.yml` builds with Jekyll 4.x and the same Gemfile you run locally — so "works on my machine" and "works on github.io" are the same thing.

### Porkbun DNS

Log in to Porkbun → Domain Management → Details on `danbratton.com` → DNS.

First, **delete any default parking records** (Porkbun adds ALIAS/A records on `@` when you register a domain — these will override the new records). Also clear the default `www` CNAME if one exists. Leave **URL Forwarding** empty.

Then add:

| Type  | Host      | Answer                    |
| ----- | --------- | ------------------------- |
| ALIAS | *(blank)* | `<username>.github.io`    |
| CNAME | `www`     | `<username>.github.io`    |

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
├── .github/workflows/
│   └── pages.yml               # Jekyll build + GitHub Pages deploy
├── CNAME                       # custom-domain marker for GitHub Pages
├── Gemfile                     # Jekyll 4.x for local preview
├── _config.yml                 # site-wide title/description/url/author
├── _layouts/
│   ├── default.html            # <!DOCTYPE>, <head> include, <body> wrapper
│   └── case-study.html         # <main>, back link, <h1>, .meta line, {{ content }}
├── _includes/
│   └── head.html               # shared <head> block with OG/Twitter meta
├── _templates/
│   └── case-study.md           # starter template — copy to projects/<slug>.md
├── index.html                  # front matter + home body (Bootstrap card grid)
├── projects/
│   └── mural-image-panel.md    # front matter + markdown body
├── assets/
│   ├── css/main.css            # editorial styles + Bootstrap card overrides
│   └── images/
│       ├── favicon.svg
│       └── image-panel.jpg     # card artwork
├── archive/                    # older notebook work, excluded from the build
└── README.md                   # (excluded from the build)
```
