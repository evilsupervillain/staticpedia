# Static-Pedia

A read-only, wiki-style encyclopedia (think Wikipedia / Fandom look and feel) delivered as **one self-contained `index.html`**. Drop it at the root of any directory tree of Markdown files on any static web host and it renders them as a browsable wiki. There is no editing, no server-side code, and no build step needed to *use* it.

## Hard requirements

- **Single file.** All of the program (JS, default CSS, Markdown parser, sanitizer, icons) lives inside `index.html`. **Zero external dependencies**: no CDN scripts, no web fonts fetched from third parties. It must work offline or air-gapped as long as the `.md` files are reachable.
- **Static hosting only.** It must work on GitHub Pages, `python -m http.server`, nginx, S3, and similar hosts. Do not assume rewrites, directory listings, or any server logic.
- **Sub-path safe.** It must work when installed at `http://example.com/wiki/` as well as at the domain root. Resolve every content URL relative to the directory containing `index.html` (derive it from `location.pathname`) and never from `/`.
- **Desktop and mobile.** The layout must be responsive. On narrow screens the sidebar collapses into a toggle and there is no horizontal page scroll.

## Routing

- The page is selected with the `p` query parameter, and the value maps to a `.md` file relative to the wiki root:
  - no `p` → `Index.md`
  - `?p=People` → `People.md`
  - `?p=People/John_Doe` or `?p=People/John_Doe.md` → `People/John_Doe.md` (the `.md` extension is optional and accepted either way)
- Optional `#fragment` scrolls to a heading anchor. Generate slug ids for all headings.
- Navigating between internal pages should use `history.pushState` (no full reload) and handle back/forward via `popstate`. A plain link to `?p=...` must also work on a fresh load.
- Missing page (404 from fetch) → render a friendly "page does not exist" page that links back to Index. Do not show a blank screen.
- Guard against path traversal and absolute URLs in `p` (reject `..` segments, leading `/`, and schemes).
- Set `document.title` from the page's first `# H1` (fallback: the filename with underscores turned into spaces), plus the site name.

## Links and media inside Markdown

- Relative links to other pages (`[Jane](People/Jane_Doe)`, `[Jane](People/Jane_Doe.md)`, `[x](../Index.md)`) are resolved **relative to the current page's directory** and rewritten to `?p=...` links.
- Wiki-style links `[[Page Name]]` and `[[People/John_Doe|John]]` are supported. Spaces become underscores.
- External links (`http(s)://`, `mailto:`, and so on) open normally. Give them `rel="noopener"` and a small external-link marker.
- Relative image and media URLs (`![](images/foo.png)`, `<video src>`, `<audio src>`, and links to non-`.md` files like PDFs) are resolved relative to the current page's directory, so they load correctly under any sub-path.

## Markdown rendering

- Use an **embedded** CommonMark-ish + GFM parser. Inlining a minified copy of a permissively licensed parser (for example marked) is fine; keep its license header. A hand-written parser is also acceptable. Required: headings, emphasis, lists (including nested and task lists), code blocks with fences, inline code, blockquotes, tables, horizontal rules, images, links, autolinks, footnotes if practical.
- **Raw HTML is allowed but sanitized.** Strip `<script>`, `<iframe>` (unless explicitly whitelisted later), `on*` event attributes, `javascript:` URLs, and similar. Use an embedded allowlist sanitizer; DOMPurify inlined with its license header is acceptable. This lets authors build infoboxes and tables in HTML.
- Optional YAML-style front matter (`---` block at the top) is stripped from the rendered output. Recognize at least `title:`.
- Auto-generate a table of contents for pages with 3 or more headings (Wikipedia-style "Contents" box). Users can toggle it in settings.

## Special files (all optional, at the wiki root)

These names are the public convention. Document them in the manual and keep them stable.

| File | Purpose |
|---|---|
| `Index.md` | Home page (the default when no `p` is given). |
| `_Sidebar.md` | If present, rendered as the left navigation panel on every page. |
| `_Footer.md` | If present, rendered as the footer on every page. |
| `style.css` | Site owner's custom styles. Loaded **after** the built-in defaults so it overrides them. A 404 is silently ignored and the built-in defaults remain. |
| `pages.json` | Optional search manifest (see Search). |
| `staticpedia.json` | Optional site config: site name, logo path, default theme, and so on. Missing means built-in defaults. |

Also decide and document the theming convention, for example `style.css` plus optional `theme-light.css` / `theme-dark.css` loaded according to the active theme. Theme hooks should be CSS custom properties on `:root`, with `[data-theme="dark"]` / `[data-theme="light"]` on `<html>`, so `style.css` can restyle everything by overriding variables.

## Search

- If `pages.json` exists, show a search box in the header that does title search plus full-text search over the manifest. If it doesn't exist, hide the search box.
- Provide a small zero-dependency generator script in the repo (`tools/build-index.mjs` for Node, plus optionally a Python equivalent) that walks the tree and writes `pages.json` containing `[{ "path": "People/John_Doe", "title": "...", "text": "plain-text excerpt or full text" }]`. Document how to run it, and how to run it in a GitHub Action.
- The generator is a convenience. The site must work fully without it.

## Settings panel

- A gear button in the **upper-right corner** opens a settings panel (a modal or drawer that is accessible, closes on Esc, and traps focus).
- Settings are persisted in `localStorage` under a single namespaced key (for example `staticpedia:settings`, a JSON object). Wrap all storage access in try/catch so the site still works when storage is blocked.
- At minimum: theme (light / dark / system), font size, content width (narrow/wide), show or hide the table of contents, and show or hide the sidebar. Keep it easy to add more.
- Apply the theme before first paint to avoid a flash of the wrong theme.

## Default look

- Clean encyclopedia styling: serif or system headings, readable body measure, a sidebar on the left, a header with the site name/logo, search, and the gear button. Style tables, code, blockquotes, and infobox-friendly floats.
- Default styles are embedded in `index.html` and must look good in both light and dark themes.
- Accessibility: semantic landmarks (`header`, `nav`, `main`, `footer`), visible focus states, sufficient contrast, and `prefers-reduced-motion` respected.

## Repo layout

The repo root **is** a live demo. Enabling GitHub Pages on `master` / root serves the manual.

```
index.html            # the entire program
style.css             # example customization (kept small; demonstrates overriding variables)
Index.md              # manual home page
_Sidebar.md
_Footer.md
staticpedia.json      # example config
pages.json            # generated search manifest for the manual
<manual pages>.md     # e.g. Installation.md, Configuration.md, Writing_Pages.md, Theming.md, Search.md, Special_Files.md, Settings.md, FAQ.md
Examples/...          # sample pages demonstrating images, tables, infobox HTML, nested folders, wiki links
images/...            # sample media used by the manual
tools/build-index.mjs # pages.json generator
README.md             # brief repo readme pointing at the manual
```

The sample documents form a **reference manual** for installing, configuring, and customizing Static-Pedia: installing at the root and under a sub-path, GitHub Pages setup, writing pages and links, media, special files, theming with `style.css`, the settings panel, search, and limitations (for example, opening `index.html` via `file://` won't work because browsers block `fetch` there, so the manual should explain how to run a local server).

## Development notes

- Test locally with `python3 -m http.server 8000` from the repo root, then also from the parent directory, visiting `/staticpedia/` to verify sub-path support.
- Keep `index.html` readable: organize it into clearly commented sections (config, router, markdown, sanitizer, rendering, settings, search). Readability matters more than minification, except for inlined third-party libraries.
- Plain modern JS (ES2020+), no frameworks, no build step for `index.html`.
