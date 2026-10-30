# Installation

Installing Static-Pedia means copying **one file**, `index.html`, into the folder that holds your Markdown. Nothing is compiled or configured on the server. This page covers getting the file, installing it at the root of a site or under a sub-path, previewing it on your own computer, and the hosting services known to work.

## What you need

- `index.html` from the [Static-Pedia repository](https://github.com/evilsupervillain/staticpedia). It is the only program file.
- A folder of Markdown files with an `Index.md` home page.
- Any host that serves static files over HTTP or HTTPS. No PHP, Node, rewrites or directory listings are required.
- Readers need a current browser: Chrome, Edge, Firefox or Safari from the last few years. Static-Pedia uses modern JavaScript (ES2020) and the `<dialog>` element.

Everything else is optional: a [`_Sidebar.md`](Special_Files#_sidebarmd) navigation panel, a [`style.css`](Theming), a [`staticpedia.json`](Configuration) config file and a [`pages.json`](Search) search index.

## Installing at the root of a site

Put `index.html` and `Index.md` at the top level of what your host publishes:

```text
public/
├── index.html
├── Index.md
└── Guides/
    └── Getting_Started.md
```

Then:

| Address | Shows |
|---|---|
| `https://example.com/` | `Index.md` |
| `https://example.com/?p=Guides/Getting_Started` | `Guides/Getting_Started.md` |
| `https://example.com/?p=Guides/Getting_Started.md` | the same page (the `.md` is optional) |
| `https://example.com/?p=Guides/` | `Guides/Index.md` |

## Installing under a sub-path

Static-Pedia never assumes it lives at `/`. It works out its own folder from the page address and loads every file relative to that folder. You can install it as deep as you like:

```text
example.com/
├── index.html            ← your normal website
└── docs/
    └── wiki/
        ├── index.html    ← Static-Pedia
        ├── Index.md
        └── People/John_Doe.md
```

Here `https://example.com/docs/wiki/?p=People/John_Doe` loads `https://example.com/docs/wiki/People/John_Doe.md`, and an image written as `![](images/a.png)` inside that page loads `/docs/wiki/People/images/a.png`.

Because of this you can move a whole wiki folder to a different path or host without changing a single link, as long as your pages use relative links (see [Links and media](Links_and_Media)).

> [!IMPORTANT]
> **Keep the trailing slash.** Link to `https://example.com/docs/wiki/`, not `.../docs/wiki`. Most hosts (GitHub Pages, nginx, Apache, Netlify, `python -m http.server`) redirect the short form for you. If yours does not, the browser treats `wiki` as a file name and relative URLs resolve one folder too high. Linking to `.../wiki/index.html` explicitly also works.

## Previewing on your own computer

Browsers refuse to let a page loaded from `file://` fetch other files, so double-clicking `index.html` cannot work. Start a tiny local web server in the wiki folder instead. Any of these will do:

```sh
# Python 3 (preinstalled on macOS and most Linux distributions)
python3 -m http.server 8000

# Node.js
npx http-server -p 8000
npx serve .

# PHP
php -S localhost:8000
```

Then open <http://localhost:8000/>. To check that your wiki also works under a sub-path, start the server in the **parent** folder and visit the wiki's folder name:

```sh
cd ..                       # the folder that contains my-wiki/
python3 -m http.server 8000
# now open http://localhost:8000/my-wiki/
```

> [!NOTE]
> `npx serve` with its `--single` (SPA) flag, and other servers that answer every unknown URL with `index.html`, still work. Static-Pedia notices when it gets an HTML page back instead of Markdown and shows its "page not found" message.

## Hosting options

Static-Pedia only needs a host that serves files as they are. These are known to work:

| Host | Notes |
|---|---|
| [GitHub Pages](GitHub_Pages) | Add an empty `.nojekyll` file, or GitHub's Jekyll step hides `_Sidebar.md` and `_Footer.md` and converts Markdown files that have front matter. See [GitHub Pages](GitHub_Pages). |
| GitLab Pages, Codeberg Pages | Publish the folder as-is. |
| Netlify, Cloudflare Pages, Vercel | Use "no build command" and publish the wiki folder. SPA fallback rules are fine but not needed. |
| Amazon S3 (+ CloudFront) | Set `index.html` as the index document. S3 answers missing files with `403` when the bucket isn't listable; Static-Pedia treats that as "page not found". |
| nginx, Apache, Caddy, IIS | Serve the folder as static files. No rewrite rules are needed. |
| Any shared web hosting | Upload with FTP/SFTP like any other files. |

### nginx

The default configuration works. Optionally give Markdown a proper content type and stop browsers caching stale pages for too long:

```nginx
location /wiki/ {
    index index.html;
    types { text/markdown md; text/html html; text/css css; application/json json; image/svg+xml svg; }
    add_header Cache-Control "no-cache";
}
```

### Apache

Works out of the box. To serve Markdown as UTF-8 text, add this to `.htaccess`:

```apache
AddType "text/markdown; charset=utf-8" .md
```

## Upgrading

Replace `index.html` with the new version. Your pages, `style.css` and `staticpedia.json` are untouched, and the public conventions (special file names, `?p=` addresses, CSS variables) are kept stable between versions.

## Checklist

- [x] `index.html` and `Index.md` are in the same folder
- [x] The folder is served over `http://` or `https://`, not opened from disk
- [x] Links to the wiki end in `/`
- [ ] *Optional:* `_Sidebar.md` for navigation ([Special files](Special_Files))
- [ ] *Optional:* `staticpedia.json` with your site name and logo ([Configuration](Configuration))
- [ ] *Optional:* `pages.json` for search ([Search](Search))
- [ ] *GitHub Pages only:* an empty `.nojekyll` file ([GitHub Pages](GitHub_Pages))
