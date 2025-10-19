# Content & Deployment Notes for this Hugo site

Short checklist you can copy-paste when adding posts/pages or changing content.

---

## Quick local workflow

1. Install Hugo (Extended recommended on Arch):

```bash
sudo pacman -Syu hugo git
# or for extended build (AUR):
# paru -S hugo-extended
```

2. Initialize theme submodule (only once or when you clone):

```bash
git submodule update --init --recursive
```

3. Run the dev server from the repository root:

```bash
hugo server -D --bind 127.0.0.1 --port 1313
```

Open http://localhost:1313 to preview changes with livereload.


## Adding a new blog post

1. Create a new content file under `content/blog/` using the archetype if needed:

```bash
# Example using the provided archetype (if present)
# If you want a markdown file directly, create content/blog/YYYY-MM-DD-my-post.md
hugo new blog/YYYY-MM-DD-my-post.md
```

2. Fill the front matter (YAML/TOML/JSON) at the top of the file. Minimum fields recommended:

```yaml
title: "My post title"
date: 2025-10-19
author: "Your Name"
summary: "One-line summary"
slug: my-post-slug
category: blog
showtoc: false
draft: true  # set to false when ready to publish
```

3. Write the content below the front matter. When ready to preview published view, set `draft: false`.

4. Preview locally with `hugo server` and test images/assets.

5. Commit and push your content branch and open a PR (or push to main if you use that flow).


## Images & page images

- Add images to the same page bundle as the content file (e.g., `content/blog/my-post/my-photo.jpg`) or reference absolute/static images in `static/images/`.
- Theme templates attempt to find images using:
  - `.Params.images` in front matter,
  - page resources matching `*feature*`, `{*cover*,*thumbnail*}`,
  - or `site.Params.images` as fallback.
- You can set `cover:` front matter fields if you want a page-specific og:image.


## When you change theme files

This project used `themes/PaperMod` as a git submodule originally.

Options:
- If you plan to edit the theme often, vendor the theme (convert submodule into a normal folder) so your edits live directly in the repo.
- If you keep it as a submodule, either fork PaperMod and commit/merge your fixes into your fork, or keep compatibility shims inside `layouts/`.


## GitHub Pages CI notes (GitHub Actions)

- Ensure CI checks out submodules: `actions/checkout@v4` with `submodules: 'recursive'` and `fetch-depth: 0`.
- Use Hugo Extended if you rely on Hugo Pipes or SCSS. Pin `hugo-version` in workflow.

Example (use this in `.github/workflows/gh-pages.yml`):

```yaml
name: Build and deploy Hugo site
on:
  push:
    branches: [ main ]

permissions:
  contents: write
  pages: write

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          submodules: 'recursive'
      - uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.151.2'
          extended: true
      - run: hugo --minify
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```


## Troubleshooting quick list

- "found no layout file" or missing templates: make sure `themes/PaperMod` exists (submodule present) or vendor the theme.
- "partial ... not found" errors: either add a shim partial in `layouts/partials/` or fix the theme partial call.
- If build logs show `deprecated: paginate`: replace `paginate` with `pagination.pagerSize` in `hugo.yaml`.
- If assets (CSS/JS) are missing: ensure you used Hugo Extended and `hugo --minify` if the theme expects it.


## Commit examples

- Add a blog post branch, preview, and then merge:

```bash
git checkout -b blog/new-post
# add content files
git add content/blog/2025-10-19-my-post.md
git commit -m "Add blog: my post"
git push origin blog/new-post
# open PR, review, merge
```


## Useful commands

```bash
# Build production static files
hugo --minify

# Serve already-built public directory (no hugo)
python3 -m http.server 1313 --directory public

# Update submodules
git submodule update --init --recursive
```


---

If you want, I can:
- Replace the repo `README.md` with this content (recommended),
- Add a Makefile with `make dev` / `make build` shortcuts, or
- Add the recommended GitHub Actions workflow file to `.github/workflows/`.

Tell me which and I'll implement it.
