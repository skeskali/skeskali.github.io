# Copilot instructions

## Build, serve, and validate

This repository is a Jekyll 3 site managed with Bundler. Install the pinned dependencies, then build the site with:

```bash
bundle install
bundle exec jekyll build
```

The generated site is written to `_site/`. For local development, use:

```bash
bundle exec jekyll serve
```

There is no test suite, test runner, or lint configuration in this repository, so there is no single-test command. Use `bundle exec jekyll build` as the primary validation check for template, Markdown, configuration, and Sass changes. The `csv-to-html/` demo is a separate static JavaScript page; serve that directory from a local HTTP server (for example, `python3 -m http.server` while `csv-to-html/` is the working directory) rather than opening it directly from `file://`, because its CSV loading uses browser requests.

## Architecture

- `_config.yml` defines three output collections and their URL shapes: pages at `/:name`, posts at `/blog/:slug`, and projects at `/project/:slug`. It also enables kramdown/GFM, Rouge syntax highlighting, pagination, sitemap generation, and compressed Sass.
- Content is split by collection: `_pages/`, `_posts/`, and `_projects/`. Front matter selects metadata such as `title`, `subtitle`, `description`, `date`, and `featured_image`; collection defaults assign the corresponding layouts automatically.
- `_layouts/default.html` is the shared document shell. It renders the header/footer, page content, SEO/social metadata, the generated stylesheet, and the minified browser assets. `page.html`, `post.html`, and `project.html` provide collection-specific wrappers; the home/blog view paginates `paginator.posts`.
- `_includes/header.html`, `footer.html`, `socials.html`, and `contact-form.html` are reusable fragments. Navigation, branding, social links, colors, typography, form behavior, and optional analytics/custom scripts are configured centrally in `_data/settings.yml`.
- `css/style.scss` is the Sass entry point. It imports foundational Sass files and component/section partials under `_sass/`, while Liquid expressions inject values from `_data/settings.yml` at build time.
- `js/journal.js` implements the theme’s optional AJAX navigation, page transitions, active-link state, gallery/carousel setup, and image-loading behavior. `js/journal-min.js` and `js/plugins-min.js` are the browser files referenced by the layout; keep source and shipped/minified assets synchronized when changing JavaScript.
- `csv-to-html/` is a self-contained static demo using vendored jQuery, Bootstrap, jQuery CSV, and DataTables assets. Its `index.html` initializes `CsvToHtmlTable` against a CSV path in its local `data/` directory; changes there do not participate in the Jekyll build.

## Repository-specific conventions

- Put new regular pages in `_pages/`, blog entries in `_posts/` with a date-prefixed filename, and portfolio entries in `_projects/` with date-prefixed filenames. Rely on collection defaults unless a page needs an explicit layout override.
- Use site-root paths for content metadata and links (for example `/images/...` or `/about`) and preserve Liquid’s `relative_url`/`absolute_url` filters in templates so the configured `baseurl` remains supported.
- Treat `_data/settings.yml` as the theme’s customization surface. Prefer adding or changing settings there and consuming them through `site.data.settings` instead of hard-coding presentation values in templates or Sass.
- Blog and project cards depend on `featured_image`; pages with a header image should provide that field. Gallery content uses literal HTML with `<div class="gallery" data-columns="N">` and image children; `data-columns="1"` is the carousel form.
- Keep the existing collection permalink conventions when linking or adding content. The projects landing page iterates `site.projects` in reverse order, while the blog landing page uses Jekyll pagination with six posts per page.
- Contact form deployment is configuration-driven: set `contact_settings.form_action`, `confirmation_url`, and `email_subject` in `_data/settings.yml`; do not put a service endpoint directly into the shared include.
- External links and browser-loaded assets are part of the theme’s existing behavior. Preserve the AJAX opt-out class (`js-no-ajax`) for links that must perform a normal navigation or browser action.
