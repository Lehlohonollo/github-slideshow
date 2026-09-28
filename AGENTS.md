# Base44 Dev Notes

## What this project is
A Jekyll-based reveal.js slide deck ("github-slideshow") built with the
`github-pages` gem. Posts in `_posts/` become slides; `index.html` iterates
them through the `presentation` layout.

## Runtime requirements (non-obvious)
- **Ruby 2.6** is required. The `Gemfile.lock` pins `github-pages (202)` →
  `jekyll 3.8.5` / `activesupport 4.2.11.1`, which do not run on Ruby 3.x.
- **Bundler 1.17.3** matches the lockfile's `BUNDLED WITH`; do not upgrade.
- The full `ruby:2.6` image (not `-slim`) is used so native extensions
  (nokogiri, ffi, unf_ext, etc.) compile via the bundled build toolchain, and
  `curl` is available for the healthcheck.
- Gem cache lives in a named volume mounted at `/bundle` with `BUNDLE_PATH=/bundle`,
  keeping the image's own bundler intact across recreations.

## Serving at the preview root
The repo's `_config.yml` sets `baseurl: "/github-slideshow"`, but the preview
loads the root path. `_config.base44.yml` overrides `baseurl: ""` and is passed
via `--config _config.yml,_config.base44.yml` so the site and its
`node_modules/reveal.js` assets resolve at `/`.

## No external credentials
This is a self-contained static site; no secrets are required to boot.

## Verify it works
- `docker compose -f docker-compose.base44.yml up -d --build`
- `curl -fsS http://localhost:3000/` should return the slideshow HTML.
- The first boot runs `bundle install` (~3–5 min); subsequent boots reuse the
  cached volume and start in seconds.
