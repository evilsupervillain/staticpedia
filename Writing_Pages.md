# Writing pages

Every page is a Markdown (`.md`) file somewhere under the folder that contains `index.html`. This page explains how file names become addresses and titles, what Markdown is supported, and how raw HTML is handled. Links, images and other media have their own page: [Links and media](Links_and_Media).

## File names and addresses

The page shown is chosen by the `p` query parameter. Its value is a path relative to the wiki root, with or without `.md`:

| Address | File loaded |
|---|---|
| `index.html` (no `p`) | `Index.md` |
| `?p=People` | `People.md` |
| `?p=People/John_Doe` | `People/John_Doe.md` |
| `?p=People/John_Doe.md` | `People/John_Doe.md` |
| `?p=People/` | `People/Index.md` |
| `?p=People/John Doe` | `People/John_Doe.md` (spaces become underscores) |
| `?p=People/John_Doe#early-life` | `People/John_Doe.md`, scrolled to the "Early life" heading |

Conventions that keep a wiki tidy:

- **Use underscores instead of spaces** in file names: `Jane_Doe.md`, not `Jane Doe.md`. Underscores are shown as spaces in titles that come from file names.
- **Case matters on most hosts.** `People.md` and `people.md` are different files on Linux servers and GitHub Pages, though not on a Mac or Windows laptop. Pick one style and stick with it.
- **A folder can have its own `Index.md`**, which is what `?p=Folder/` and links to `Folder/` show.
- Names starting with `_` are reserved for [special files](Special_Files) and are skipped by the [search index generator](Search).

Requests that try to escape the wiki are refused with an "Invalid page name" message: values starting with `/`, containing `..` segments or backslashes, or that look like URLs (`http:`, `javascript:` and so on).

If the file does not exist, readers get a friendly "Page not found" page with a link back to the main page and, when search is available, suggestions.

## Page titles

The big heading at the top of a page, and the browser tab title, are chosen like this:

1. **Heading:** the page's first `# Heading 1`. If the page has no level-1 heading, Static-Pedia adds one from the front matter `title`, or else from the file name (`John_Doe` → "John Doe"; a folder's `Index.md` uses the folder name, and the root `Index.md` uses the site name).
2. **Tab title:** front matter `title` if present, otherwise the first `# Heading 1`, otherwise the file name, followed by ` — ` and the site name. On the page whose title equals the site name (like this manual's home page) the tab shows just the site name.

Start most pages with exactly one `# Title` line and use `##` and below for sections, as Wikipedia does.

## Front matter

A page may start with a YAML-style block between `---` lines. It is not shown on the page.

```markdown
---
title: John Doe (cartographer)
toc: false
noindex: true
---

# John Doe
...
```

| Key | Effect |
|---|---|
| `title` | Browser tab title, the heading when the page has no `# H1`, and the title in search results. |
| `toc` | `false` hides the table of contents on this page. |
| `noindex` | `true` keeps the page out of `pages.json`, so it never appears in search. (Used by the generator only.) |

Other keys are allowed and ignored, so you can keep your own metadata there. Only simple `key: value` lines are understood, not nested YAML.

## Headings, anchors and the table of contents

Every heading gets an `id` so you can link to it. The id is the heading text in lower case, with punctuation removed and spaces turned into dashes:

| Heading | Anchor |
|---|---|
| `## Early life` | `#early-life` |
| `## Works (1990–2001)` | `#works-19902001` |
| `### What's new?` | `#whats-new` |

Duplicates get `-1`, `-2` and so on. Hover over a heading to reveal a `#` link you can copy.

Pages with **three or more section headings** get a Wikipedia-style **Contents** box just before the first section. Readers can hide it everywhere from the [settings panel](Settings), you can turn it off for one page with `toc: false` in the front matter, and the site owner can change the threshold with [`tocMinHeadings`](Configuration). Headings inside tables, block quotes, `<details>`, callouts, infoboxes or any element with class `no-toc` are left out of the box.

## Markdown syntax

Static-Pedia includes its own parser for CommonMark plus the GitHub extensions people expect. See the [Markdown showcase](Examples/Markdown_Showcase) for every feature rendered live.

| You write | You get |
|---|---|
| `*italic*` or `_italic_` | *italic* |
| `**bold**` | **bold** |
| `~~struck~~` | ~~struck~~ |
| `` `code` `` | `code` |
| `[link](Writing_Pages)` | [link](Writing_Pages) |
| `[[Writing Pages]]` | [[Writing Pages]] |
| `https://example.com` | https://example.com |
| `![alt](images/logo.svg)` | an image |
| `- item` / `1. item` | bulleted / numbered lists, nested by indenting |
| `- [ ] todo` / `- [x] done` | task lists |
| `> quote` | block quote |
| `> [!NOTE]` | a note callout (also `TIP`, `IMPORTANT`, `WARNING`, `CAUTION`) |
| ```` ```js ```` | fenced code block (the language becomes a `language-js` class) |
| `---` | horizontal rule |
| `\| a \| b \|` | tables, with `:--:` alignment |
| `text[^1]` + `[^1]: note` | footnotes, collected at the end of the page |

## Raw HTML

You can mix HTML into Markdown, which is how [infoboxes](Examples/People/John_Doe), figures with captions and complex tables are built:

```html
<table class="infobox">
  <caption>Example</caption>
  <tr><th>Born</th><td>1 January 1970</td></tr>
</table>
```

A block of HTML ends at the first blank line. Leave a blank line after an opening tag if you want Markdown to be processed inside it:

```markdown
<div class="notice">

This **is** Markdown, because of the blank lines around it.

</div>
```

### What the sanitizer removes

All HTML, whether written by hand or produced from Markdown, passes through a built-in allowlist sanitizer before it reaches the page. That makes it safe to publish pages from many authors:

- **Removed with their content:** `<script>`, `<style>`, `<iframe>` (unless its host is allowed, see [Links and media](Links_and_Media#embedding-iframes)), `<object>`, `<embed>`, `<form>` and other form controls, `<svg>`, `<math>`, `<template>`, `<link>`, `<meta>` and `<base>`.
- **Removed attributes:** every event handler (`onclick`, `onerror`…), `name`, `form` attributes, `style` values containing `expression(`, `javascript:` or `@import`, and any attribute not on the allowlist.
- **Removed URLs:** `javascript:`, `vbscript:` and `data:` links. `data:image/...` is still allowed in `<img>`.
- **Unwrapped:** unknown tags such as `<font>` or `<marquee>` are removed but their text is kept.
- **Kept:** headings, paragraphs, lists, tables, `<div>`, `<span>`, `<details>`/`<summary>`, `<figure>`/`<figcaption>`, `<img>`, `<picture>`, `<video>`, `<audio>`, `<source>`, `<track>`, `<abbr>`, `<kbd>`, `<mark>`, `<sub>`, `<sup>`, `<ruby>` and friends, plus `id`, `class`, `title`, `style`, `lang`, `dir`, `role`, `aria-*` and `data-*` attributes.
- Links with `target="_blank"` automatically get `rel="noopener noreferrer"`.
- Ids starting with `sp-` are reserved for Static-Pedia itself and are removed from content.

## Classes for encyclopedia layouts

The default stylesheet understands a handful of classes. [Theming](Theming#content-classes) lists them all.

| Class | Use |
|---|---|
| `infobox` | Floating summary box on the right (`<table class="infobox">` or a `<div>`) |
| `thumb`, `right`, `left`, `center` on `<figure>` | Framed images with captions |
| `float-right`, `float-left`, `center`, `clear` | Simple layout helpers |
| `hatnote` | Italic "For other uses, see…" line under a title |
| `notice` | Boxed message, like Wikipedia's article message boxes |
| `plain` on a `<table>` | Table without the default borders |
| `no-toc` | Keep the headings inside this element out of the contents box |
