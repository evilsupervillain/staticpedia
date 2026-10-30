# GitHub Pages

[GitHub Pages](https://pages.github.com/) is the easiest free home for a Static-Pedia wiki. Your Markdown lives in a repository, people can propose edits with pull requests, and every push republishes the site. This manual is published exactly this way.

## Publish from a branch

1. Create a repository, or use an existing one, and add `index.html`, `Index.md` and your other pages.
2. **Add an empty file called `.nojekyll`** at the root of what you publish (see [below](#why-nojekyll-matters)).
3. Push to GitHub.
4. In the repository, open **Settings → Pages**.
5. Under **Build and deployment**, choose **Source: Deploy from a branch**, pick your branch (for example `master` or `main`) and the folder (`/ (root)` or `/docs`), and click **Save**.
6. After a minute the site is live at `https://<user>.github.io/<repository>/`.

The address has a sub-path (`/<repository>/`), which Static-Pedia handles without any configuration. With a [custom domain](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site) the same files are served from the root instead. Nothing changes on your side.

### Keeping the wiki in a sub-folder

If the repository also contains other things, put the wiki in `docs/` and choose `/docs` as the publishing folder. Only that folder is published, so `index.html`, `.nojekyll` and the pages all go inside `docs/`.

## Why `.nojekyll` matters

By default GitHub Pages runs every site through [Jekyll](https://jekyllrb.com/). For a Static-Pedia wiki that causes two problems:

- Jekyll **does not publish files or folders whose names start with `_`**, so `_Sidebar.md` and `_Footer.md` disappear.
- Jekyll **converts Markdown files that have front matter into HTML**, so `?p=` can no longer fetch the raw `.md` and you get "page not found".

An empty `.nojekyll` file at the published root turns Jekyll off and the files are served exactly as committed:

```sh
touch .nojekyll
git add .nojekyll
git commit -m "Serve files as-is on GitHub Pages"
```

## Building the search index automatically

The search box appears when the site has a `pages.json` file. You can regenerate it on every push with GitHub Actions instead of remembering to run the [generator](Search#generating-pagesjson) yourself.

### Option A: commit `pages.json` back to the branch

This keeps "Deploy from a branch" publishing. Save as `.github/workflows/search-index.yml`:

```yaml
name: Update search index
on:
  push:
    branches: [master]
    paths: ['**.md', 'staticpedia.json']
  workflow_dispatch:

permissions:
  contents: write

jobs:
  index:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: node tools/build-index.mjs
      - name: Commit pages.json if it changed
        run: |
          if ! git diff --quiet -- pages.json; then
            git config user.name "github-actions[bot]"
            git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
            git add pages.json
            git commit -m "Update search index"
            git push
          fi
```

The `paths` filter means the workflow's own commit (which changes only `pages.json`) does not trigger it again.

### Option B: build during deployment

If you publish with **Source: GitHub Actions**, generate the index in the deploy job and never commit it:

```yaml
name: Deploy wiki
on:
  push:
    branches: [master]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: node tools/build-index.mjs
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: .
      - id: deployment
        uses: actions/deploy-pages@v4
```

Artifacts uploaded this way are not processed by Jekyll, but keeping `.nojekyll` does no harm.

> [!TIP]
> To make a pull request fail when someone forgets to rebuild the index, run `node tools/build-index.mjs --check` in CI. It exits with an error if `pages.json` is out of date and changes nothing.

## Editing on GitHub

Because pages are ordinary Markdown files, GitHub's web editor and pull requests work as your wiki's editing workflow. GitHub's own Markdown preview is close to what Static-Pedia renders. The differences are the `[[wiki link]]` syntax and the [link rules](Links_and_Media) (GitHub's preview does not rewrite `?p=` links).
