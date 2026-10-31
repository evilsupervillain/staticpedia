# Static-Pedia

A read-only, wiki-style encyclopedia delivered as **one self-contained `index.html`**. Drop it at the root of any folder of Markdown files on any static web host, and the files become a browsable wiki with a sidebar, search, a table of contents, and light and dark themes.

- **Single file, zero dependencies.** The Markdown parser, HTML sanitizer, styles and icons are all inside `index.html`. It works offline and air-gapped.
- **Static hosting only.** GitHub Pages, `python -m http.server`, nginx, S3, Netlify… at the domain root or under any sub-path.
- **Encyclopedia look**, responsive down to phones, with a settings panel for theme, text size, width, contents box and sidebar.

## Quick start

```sh
cp index.html my-wiki/          # next to my-wiki/Index.md
cd my-wiki
python3 -m http.server 8000     # then open http://localhost:8000/
```

`?p=People/John_Doe` shows `People/John_Doe.md`. Opening `index.html` straight from disk (`file://`) won't work, because browsers block the requests it needs.

## The manual

This repository is itself a Static-Pedia wiki: the Markdown files at the root are the reference manual. Read it by serving the repository locally (above), or on GitHub Pages (Settings → Pages → deploy from `master`, root).

| Topic | File |
|---|---|
| Start here | [Index.md](Index.md) |
| Installing, sub-paths, hosting | [Installation.md](Installation.md), [GitHub_Pages.md](GitHub_Pages.md) |
| Writing pages, links, media | [Writing_Pages.md](Writing_Pages.md), [Links_and_Media.md](Links_and_Media.md) |
| `_Sidebar.md`, `_Footer.md`, `style.css`, … | [Special_Files.md](Special_Files.md) |
| `staticpedia.json` | [Configuration.md](Configuration.md) |
| Styling and dark mode | [Theming.md](Theming.md) |
| Settings panel | [Settings.md](Settings.md) |
| Search and `pages.json` | [Search.md](Search.md) |
| Troubleshooting and limitations | [FAQ.md](FAQ.md) |

Sample pages live in [`Examples/`](Examples/Index.md).

## Repository layout

```text
index.html            the entire program
style.css             example customization (overrides CSS variables)
staticpedia.json      example site configuration
_Sidebar.md           navigation panel
_Footer.md            footer
.nojekyll             makes GitHub Pages serve files as-is
pages.json            generated search index for the manual
*.md                  the manual
Examples/             sample pages, images and media
images/               images used by the manual
tools/build-index.mjs pages.json generator (Node.js, no dependencies)
tools/build_index.py  the same generator in Python
```

Rebuild the search index after editing pages:

```sh
node tools/build-index.mjs        # or: python3 tools/build_index.py
```

## License

MIT.
