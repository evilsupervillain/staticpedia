# Links and media

Static-Pedia rewrites every link and media URL in a page so it works wherever the wiki is installed, whether that is the root of a domain or `/some/deep/sub-path/`. This page explains the rules.

## Links to other pages

Write ordinary Markdown links with paths **relative to the current page's folder**. The `.md` extension is optional:

| Written in `People/John_Doe.md` | Goes to |
|---|---|
| `[Jane](Jane_Doe)` | `People/Jane_Doe.md` |
| `[Jane](Jane_Doe.md)` | `People/Jane_Doe.md` |
| `[Jane](./Jane_Doe.md)` | `People/Jane_Doe.md` |
| `[Home](../Index.md)` | `Index.md` |
| `[Tables](../Examples/Tables)` | `Examples/Tables.md` |
| `[Everyone](./)` | `People/Index.md` |
| `[Install](/Installation)` | `Installation.md`: a leading `/` means **the wiki root**, not the host root |
| `[Early life](Jane_Doe#early-life)` | `People/Jane_Doe.md`, scrolled to that heading |
| `[See below](#references)` | a heading on the same page |

Each of these becomes a `?p=...` link. Clicking it swaps the page in place without reloading, and Back/Forward work normally. Opening it in a new tab or sharing it also works, because the address is a real URL.

Writing the links this way keeps your Markdown readable on GitHub and in editors, which understand relative `.md` links too.

### Page or file?

A relative link is treated as a **page** when it ends in `.md`, ends in `/`, or has no file extension. Anything with another extension (`report.pdf`, `data.csv`, `photo.jpg`) is treated as a **file** and linked directly, relative to the page's folder. A page name that contains a dot, like `Version_2.0`, still counts as a page because `.0` has no letters. If in doubt, add `.md`.

### Links with query strings

Links starting with `?` are passed through, so you can link to built-in pages:

| Link | Shows |
|---|---|
| `[All pages](?special=AllPages)` | every page in the search index, grouped by folder |
| `[Random page](?special=Random)` | a random page from the search index |
| `[Search for cats](?search=cats)` | search results |

The special pages need [`pages.json`](Search).

## Wiki links

Double square brackets link to a page by name, the way MediaWiki and GitHub wikis do:

| You write | Goes to | Shows |
|---|---|---|
| `[[Installation]]` | `Installation.md` | Installation |
| `[[Writing Pages]]` | `Writing_Pages.md` | Writing Pages |
| `[[Examples/People/John_Doe\|John]]` | `Examples/People/John_Doe.md` | John |
| `[[Theming#CSS variables]]` | `Theming.md` at `#css-variables` | Theming#CSS variables |
| `[[#Wiki links]]` | this page's "Wiki links" heading | #Wiki links |

Spaces become underscores. Unlike relative Markdown links, **wiki link targets are always resolved from the wiki root**, so `[[People/John_Doe]]` means the same thing on every page. The text after `|` is the label. Inside a table, write the bar as `\|` so it doesn't split the cell.

See them in action on [Wiki links](Examples/Wiki_Links).

## External links

Links with a scheme (`https://`, `http://`, `mailto:`, `tel:`…) or starting with `//` open normally. They get `rel="noopener"` and a small arrow icon so readers can tell they are leaving the wiki: [example.com](https://example.com). Bare URLs such as https://example.com and `<https://example.com>` become links automatically.

To open a link in a new tab, use HTML: `<a href="https://example.com" target="_blank">`. Static-Pedia adds `rel="noopener noreferrer"`.

## Images

Image paths are relative to the page's folder too:

```markdown
![The Static-Pedia logo](images/logo.svg "Optional title")
```

![The Static-Pedia logo](images/logo.svg "The Static-Pedia logo")

Written in `People/John_Doe.md`, `![](images/portrait.svg)` loads `People/images/portrait.svg`, and `![](/images/logo.svg)` loads `images/logo.svg` from the wiki root. Images are never wider than the text column and load lazily.

For sizes, captions and floating, use HTML:

```html
<figure class="thumb right">
  <img src="images/logo.svg" alt="Logo" width="120">
  <figcaption>A framed, captioned image floating on the right.</figcaption>
</figure>
```

`<picture>`, `srcset` and `sizes` work too, and every URL in them is rewritten the same way.

## Video and audio

Use the HTML elements directly. `src`, `poster` and the `src` of every `<source>` and `<track>` are resolved relative to the page:

```html
<video controls width="480" poster="media/poster.jpg">
  <source src="media/clip.webm" type="video/webm">
  <source src="media/clip.mp4" type="video/mp4">
  <track src="media/clip.en.vtt" kind="subtitles" srclang="en" label="English">
</video>

<audio controls src="media/chime.wav"></audio>
```

[Images and media](Examples/Images_and_Media) shows working examples.

## Links to files

Link to PDFs, spreadsheets, archives or anything else like any other relative link:

```markdown
[Download the data (CSV)](files/elements.csv)
```

The browser opens or downloads the file itself. Add `download` in HTML (`<a href="files/elements.csv" download>`) to force a download.

## Embedding iframes

`<iframe>` is removed by default. To allow embeds from specific services, list their host names in [`staticpedia.json`](Configuration):

```json
{ "iframeHosts": ["www.youtube-nocookie.com", "player.vimeo.com"] }
```

Then, in a page:

```html
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/VIDEO_ID" title="Video title"></iframe>
```

Only `https://` URLs whose host exactly matches an entry are kept. Static-Pedia always adds a `sandbox` attribute, lazy loading and a strict referrer policy, and drops every other attribute except `width`, `height`, `title`, `class` and `style`.

> [!WARNING]
> Embeds load content from another site, so they don't work on an offline or air-gapped wiki and they let that site see who reads the page. Allow only hosts you trust.
