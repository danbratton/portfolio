source "https://rubygems.org"

# Jekyll 4.x runs cleanly on modern Ruby. We deploy via a GitHub Actions
# workflow (.github/workflows/pages.yml) rather than the legacy "Deploy from
# a branch" mode, so we're free to pick any Jekyll version — we don't need
# to pin to whatever the `github-pages` gem pretends GitHub runs.
gem "jekyll", "~> 4.3"

# Ruby 3+ removed webrick from the standard library, and Jekyll's dev server
# still expects it.
gem "webrick", "~> 1.8"

# Ruby 3.4+ removed several default gems that Jekyll's dependencies still
# require from stdlib.
gem "csv"
gem "base64"
gem "bigdecimal"
gem "logger"
