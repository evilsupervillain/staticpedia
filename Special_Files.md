# Special files

A few file names at the **wiki root** (the folder containing `index.html`) have special meaning. All of them are optional except `Index.md`, and a missing one is never an error. These names are a stable public convention.

| File | Purpose |
|---|---|
| `Index.md` | Home page, shown when there is no `p` in the address. |
| `_Sidebar.md` | Rendered as the left navigation panel on every page. |
| `_Footer.md` | Rendered as the footer on every page. |
| `style.css` | Your custom styles, loaded **after** the built-in defaults so they win. |
| `pages.json` | Search manifest. When it exists, the header shows a search box. |
| `staticpedia.json` | Site configuration: name, logo, default theme and more. |
| `.nojekyll` | Not read by Static-Pedia, but needed on [GitHub Pages](GitHub_Pages#why-nojekyll-matters). |

Two more files are loaded only if `staticpedia.json` names them: per-theme stylesheets ([`themeStylesheets`](Theming#per-theme-stylesheets)) and extra stylesheets ([`stylesheets`](Configuration)).

## `Index.md`

The front page of the wiki. Any folder can also have an `Index.md`, which is shown for `?p=Folder/` and for relative links that end in `/`, such as `[People](People/)`.

## `_Sidebar.md`

Ordinary Markdown shown in the navigation panel to the left of every page. Links in it are relative to the wiki root, whichever page is being viewed. The link to the current page is highlighted. If there is no `_Sidebar.md`, the panel and its menu button are hidden and pages use the full width.

A typical sidebar is a few short lists under small headings:

```markdown
- [Main page](Index)
- [All pages](?special=AllPages)

### People
- [John Doe](People/John_Doe)
- [Jane Doe](People/Jane_Doe)

### Places
- [[Places/Springfield|Springfield]]
```

Headings in the sidebar are styled as small section labels, and lists have no bullets. On narrow screens the sidebar collapses behind the ☰ menu button. On wide screens the same button hides or shows it (readers can also do that in [settings](Settings)).

## `_Footer.md`

Markdown shown at the bottom of every page, below the article. It is a good place for a copyright line, a licence or contact details. Links in it are relative to the wiki root. Without a `_Footer.md` the footer shows the [`footerText`](Configuration) setting ("Powered by Static-Pedia." by default). Set `footerText` to an empty string to show nothing.

## `style.css`

Loaded right after Static-Pedia's built-in styles, on every page. It can override any CSS variable or rule. If the file doesn't exist the request fails quietly (your browser's developer console notes a 404) and the default look remains. See [Theming](Theming).

## `pages.json`

An optional search manifest:

```json
[
  { "path": "People/John_Doe", "title": "John Doe", "text": "John Doe is a fictional…" }
]
```

When present, Static-Pedia shows the search box, enables the [`?special=AllPages`](?special=AllPages) and [`?special=Random`](?special=Random) pages, and suggests similar pages on "not found" pages. Generate it with `node tools/build-index.mjs`. See [Search](Search).

## `staticpedia.json`

Site-wide settings such as the site name, logo and default theme. All keys are optional, unknown keys are ignored, and invalid JSON is reported in the console and otherwise ignored. See [Configuration](Configuration) for every key.

## Other conventions

- Files and folders whose names start with `_` or `.` are skipped by the search index generator. Use them for drafts, templates or partials you don't want in search. They can still be opened with `?p=`.
- Folders are just folders. A file in `People/` is addressed as `?p=People/Name`. There are no special category files.
