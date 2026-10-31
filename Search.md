# Search

Static-Pedia has no server to run searches on, so it searches a **manifest file**, `pages.json`, that lists every page with its title and text. When `pages.json` exists at the wiki root, a search box appears in the header. Without it the search box stays hidden and everything else works as normal.

## Using search

- Type in the search box (or press <kbd>/</kbd> to jump to it). Up to eight matching pages appear as you type, best first.
- <kbd>↑</kbd> and <kbd>↓</kbd> move through the suggestions, <kbd>Enter</kbd> opens the highlighted one, and <kbd>Esc</kbd> closes the list.
- <kbd>Enter</kbd> with nothing highlighted, or **Search all pages for…**, opens the full results page with a text snippet for each match. Its address is `?search=your+words`, so searches can be bookmarked and shared, for example [`?search=sidebar`](?search=sidebar).

Every word must appear in a page's title, path or text. Matching ignores case and accents ("cafe" finds "Café"). Pages whose title matches rank first, then pages that mention the words most often.

The manifest also powers two special pages: [All pages](?special=AllPages) (`?special=AllPages`), a list of every page grouped by folder, and [Random page](?special=Random) (`?special=Random`). "Page not found" pages suggest similarly named pages too.

## The `pages.json` format

A JSON array with one object per page:

```json
[
  {
    "path": "People/John_Doe",
    "title": "John Doe",
    "text": "John Doe (born 1970) is a fictional cartographer…"
  }
]
```

| Field | Required | Meaning |
|---|---|---|
| `path` | yes | The page's path as used in `?p=`, without `.md` (a trailing `.md` is tolerated). |
| `title` | no | Display title. Defaults to the file name with underscores as spaces. |
| `text` | no | Plain text to search: an excerpt or the whole page. |

The file is downloaded once per visit, so keep it reasonable. A few megabytes is fine for most readers; the generator's `--max-chars` option limits text per page. `{ "pages": [...] }` is accepted as well as a bare array.

## Generating `pages.json`

The repository includes a small generator with **no dependencies**. Run it from the wiki root:

```sh
node tools/build-index.mjs
```

```text
Wrote 19 pages to pages.json
```

It needs Node.js 18 or newer. There is a Python equivalent that produces the same file, for machines without Node:

```sh
python3 tools/build_index.py
```

The generator walks the folder, reads every `.md` file and records:

- **path**: the file's path without `.md`.
- **title**: the front matter `title`, else the first `# Heading`, else the file name. This matches what Static-Pedia shows.
- **text**: the page as plain text, with Markdown syntax, HTML tags, front matter and link targets removed, and code blocks kept.

It skips files and folders whose names start with `.` or `_` (so `_Sidebar.md` and `_Footer.md` are never results), `node_modules`, and pages with `noindex: true` in their front matter.

### Options

| Option | Meaning |
|---|---|
| `node tools/build-index.mjs path/to/wiki` | Index a different folder (`pages.json` is written inside it). |
| `--out FILE` | Write somewhere else. |
| `--max-chars N` | Keep at most `N` characters of text per page (default `20000`, `0` for no limit). |
| `--exclude GLOB` | Leave out matching files. Repeatable. `*` matches within a folder, `**` across folders: `--exclude "Drafts/**" --exclude "*.draft.md"`. |
| `--check` | Write nothing. Exit with status 1 if `pages.json` is missing or out of date. Useful in CI. |
| `--quiet` | Print nothing on success. |

Patterns can also be listed in [`staticpedia.json`](Configuration) under `searchExclude`, which is how this manual keeps `README.md` out of its index:

```json
{ "searchExclude": ["README.md", "CLAUDE.md"] }
```

## Keeping the index up to date

`pages.json` is only as fresh as the last time you ran the generator. Common setups:

- **Run it before you commit**, or add it to a Git pre-commit hook.
- **Let GitHub Actions do it** on every push. [GitHub Pages](GitHub_Pages#building-the-search-index-automatically) has ready-to-use workflows that either commit `pages.json` back or build it during deployment.
- **Check it in CI** with `node tools/build-index.mjs --check`.

A stale index never breaks the wiki: a page missing from it is just not found by search, and a result for a deleted page leads to the friendly "page not found" page.

## Without the generator

The generator is a convenience. You can write `pages.json` by hand, produce it from another tool or CMS, or leave it out entirely. Static-Pedia works fully without it; you just don't get the search box and the special pages.
