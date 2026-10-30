# Configuration

Site-wide options live in an optional **`staticpedia.json`** file at the wiki root. Every key is optional: leave the file out entirely and the built-in defaults apply. Unknown keys are ignored. If the file isn't valid JSON, a warning goes to the browser console and the defaults are used.

## Example

This is the configuration of the manual you are reading:

```json
{
  "siteName": "Static-Pedia",
  "tagline": "The single-file wiki engine",
  "logo": "images/logo.svg",
  "favicon": "images/logo.svg",
  "lang": "en",
  "defaultTheme": "system",
  "defaults": {
    "fontSize": "medium",
    "width": "narrow",
    "toc": true,
    "sidebar": true
  },
  "tocMinHeadings": 3,
  "showSourceLink": true,
  "showLastModified": true,
  "iframeHosts": ["www.youtube-nocookie.com"],
  "searchExclude": ["README.md", "CLAUDE.md"]
}
```

## All keys

Paths are always relative to the wiki root.

| Key | Default | Description |
|---|---|---|
| `siteName` | `"Static-Pedia"` | Shown in the header and appended to every browser tab title. |
| `tagline` | `""` | Small line under the site name (hidden on phones). |
| `logo` | `""` | Image shown left of the site name, e.g. `"images/logo.svg"`. About 40 px tall. |
| `favicon` | built-in icon | Browser tab icon, e.g. `"images/logo.svg"` or `"favicon.png"`. |
| `lang` | `"en"` (from `index.html`) | The `lang` attribute of the page, e.g. `"de"`. Helps screen readers and hyphenation. |
| `defaultTheme` | `"system"` | `"system"`, `"light"` or `"dark"`. Shortcut for `defaults.theme`. |
| `defaults` | `{}` | Default values for the [settings panel](Settings): `theme`, `fontSize` (`small`, `medium`, `large`, `xlarge`), `width` (`narrow`, `wide`), `toc` and `sidebar` (`true`/`false`). Readers' own choices override them. |
| `titleSeparator` | `" — "` | Text between the page title and the site name in the tab title. |
| `tocMinHeadings` | `3` | Pages with at least this many section headings get a table of contents. |
| `showSourceLink` | `true` | Show a "View source" link to the page's `.md` file under each article. |
| `showLastModified` | `true` | Show "This page was last edited on …" when the server sends a `Last-Modified` header. |
| `iframeHosts` | `[]` | Host names allowed in `<iframe src>`. See [embedding iframes](Links_and_Media#embedding-iframes). |
| `stylesheets` | `[]` | Extra CSS files to load after `style.css`, e.g. `["css/print.css"]`. |
| `themeStylesheets` | `{}` | CSS files loaded only for one theme: `{ "light": "theme-light.css", "dark": "theme-dark.css" }`. See [Theming](Theming#per-theme-stylesheets). |
| `footerText` | `"Powered by Static-Pedia."` | Footer text used when there is no `_Footer.md`. `""` hides it. |
| `searchExclude` | `[]` | Read only by the [search index generator](Search): glob patterns of files to leave out, like `"Drafts/**"`. |

## When configuration is applied

`staticpedia.json` is fetched once per visit, in parallel with the first page. Changing it takes effect on the next page load.

Site defaults for settings (`defaultTheme` and `defaults`) are also remembered in the reader's browser, so the next visit applies them **before the first paint** with no flash of the wrong theme. On the very first visit a reader may briefly see the system theme before a different `defaultTheme` kicks in.

## Things that are not configurable

Some behaviour is fixed so that every Static-Pedia wiki works the same way:

- The home page is always `Index.md`, and pages are always addressed with `?p=`.
- The special file names (`_Sidebar.md`, `_Footer.md`, `style.css`, `pages.json`, `staticpedia.json`) cannot be renamed.
- The settings storage key is always `staticpedia:settings` (see [Settings](Settings#how-settings-are-stored)).
