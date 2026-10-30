# Theming

Static-Pedia's built-in look is a clean encyclopedia style with a light and a dark theme. You can change all of it with a **`style.css`** file next to `index.html`. Usually you only need to override a few CSS variables.

## How styles are loaded

In order, each layer overriding the previous ones:

1. **Built-in styles** inside `index.html`.
2. **`style.css`** at the wiki root, if it exists.
3. **Extra stylesheets** listed in `stylesheets` in [`staticpedia.json`](Configuration).
4. **The per-theme stylesheet** for the active theme, if `themeStylesheets` names one ([below](#per-theme-stylesheets)).

Everything must be served from your own site: Static-Pedia never fetches fonts or styles from third parties. If you want a web font, put the font file in your wiki folder and declare it with `@font-face` in `style.css`.

## Light and dark themes

The active theme is written on the `<html>` element before the page is drawn:

```html
<html data-theme="dark" data-theme-pref="system">
```

`data-theme` is always `light` or `dark`. It is the theme actually shown, after resolving "System" against the reader's operating system. `data-theme-pref` is the reader's choice (`system`, `light` or `dark`). So:

```css
/* Applies in both themes */
:root { --sp-accent: #0b7a75; }

/* Only in the light theme */
:root[data-theme="light"] { --sp-bg: #fbf8f1; }

/* Only in the dark theme */
:root[data-theme="dark"] { --sp-bg: #0d1117; }
```

Built-in dark values are set with `:root[data-theme="dark"]`. Use at least that specificity to override them; a plain `:root` rule in `style.css` applies to both themes but loses to the built-in dark values.

## CSS variables

### Colors

| Variable | Used for | Light | Dark |
|---|---|---|---|
| `--sp-bg` | Page background around the article, header, sidebar | `#f6f6f6` | `#101418` |
| `--sp-surface` | Article background, dialogs, dropdowns | `#ffffff` | `#171b20` |
| `--sp-surface-2` | Infoboxes, contents box, tables, callouts | `#f8f9fa` | `#1f242a` |
| `--sp-surface-3` | Table headers, hover states | `#eaecf0` | `#2a3038` |
| `--sp-text` | Body text | `#202122` | `#e8eaed` |
| `--sp-text-muted` | Secondary text, captions, footer | `#54595d` | `#a8b0b9` |
| `--sp-border` | Strong borders (tables, inputs) | `#a2a9b1` | `#5b636c` |
| `--sp-border-light` | Subtle borders, heading rules | `#c8ccd1` | `#343b43` |
| `--sp-link` | Links | `#3366cc` | `#88a3e8` |
| `--sp-link-visited` | Visited links | `#6b4ba1` | `#b9a1ec` |
| `--sp-link-hover` | Hovered links | `#447ff5` | `#aac0f5` |
| `--sp-link-new` | Links with class `new` (red links) | `#d73333` | `#ff8a80` |
| `--sp-accent` | Buttons, active states, focus | `#3366cc` | `#6d8fe0` |
| `--sp-accent-text` | Text on accent backgrounds | `#ffffff` | `#0b0e11` |
| `--sp-focus` | Keyboard focus outline | `#3366cc` | `#88a3e8` |
| `--sp-header-bg` | Header background | `var(--sp-bg)` | |
| `--sp-sidebar-bg` | Sidebar background (desktop) | `var(--sp-bg)` | |
| `--sp-input-bg` | Search box background | `#ffffff` | `#0c0f12` |
| `--sp-code-bg`, `--sp-code-text` | Inline code and code blocks | `#f3f4f6`, `#000` | `#22272e`, `#e8eaed` |
| `--sp-mark-bg` | Search highlights, targeted footnotes | `#fef6b5` | `#5c4f12` |
| `--sp-note`, `--sp-tip`, `--sp-important`, `--sp-warning`, `--sp-caution` | Callout colors | | |
| `--sp-shadow` | Dropdown and dialog shadows | | |

### Typography and layout

| Variable | Default | Used for |
|---|---|---|
| `--sp-font-body` | system UI font stack | Body text and interface |
| `--sp-font-heading` | `"Linux Libertine", Georgia, …, serif` | Page title, `h1`/`h2`, site name |
| `--sp-font-mono` | `ui-monospace, …, monospace` | Code |
| `--sp-font-scale` | `1` (set by the text-size setting) | Scales the root font size |
| `--sp-line-height` | `1.6` | Body line height |
| `--sp-page-max` | `100rem` | Maximum width of the whole layout |
| `--sp-sidebar-width` | `11.5rem` | Sidebar width on wide screens |
| `--sp-content-max` | `58rem` (`none` in "wide" mode) | Maximum width of the article text |
| `--sp-radius` | `4px` | Corner radius of boxes and inputs |

## Settings hooks

Each [reader setting](Settings) is also an attribute on `<html>`, so you can style around them:

| Attribute | Values |
|---|---|
| `data-theme` / `data-theme-pref` | see above |
| `data-font-size` | `small`, `medium`, `large`, `xlarge` |
| `data-width` | `narrow`, `wide` |
| `data-toc` | `on`, `off` |
| `data-sidebar` | `on`, `off` |

For example, a wider measure in narrow mode:

```css
:root[data-width="narrow"] { --sp-content-max: 64rem; }
```

## Per-theme stylesheets

If your theme customizations are large, keep them in separate files and name them in `staticpedia.json`:

```json
{ "themeStylesheets": { "light": "theme-light.css", "dark": "theme-dark.css" } }
```

The matching file is loaded after `style.css` and swapped whenever the theme changes. Rules in it don't need a `[data-theme]` selector, but variables still need enough specificity to beat the built-in dark values, so use `:root[data-theme]` in them.

## Content classes

These classes are styled by default and can be used in raw HTML in any page:

| Class | Element | Effect |
|---|---|---|
| `infobox` | `table` or `div` | Right-floating summary box. Inside: `caption` or `.infobox-title` for the name, `th.infobox-header` for section rows, `td.infobox-image` for a centered picture, `.infobox-caption` for its caption. Full width on phones. |
| `thumb` | `figure` | Framed figure with caption. |
| `right`, `left`, `center` | `figure` | Floated (and framed) figure, or a centered one. |
| `float-right`, `float-left` | any | Float without a frame. |
| `center` | any | Center the content. |
| `clear` | any | Start below all floats. |
| `hatnote` | `p`, `div` | Indented italic note under a title ("For other uses…"). |
| `notice` (or `ambox`) | `div` | Boxed message with a colored left edge. |
| `plain` | `table` | No borders or background. |
| `new` | `a` | Red "page doesn't exist yet" link color. |
| `no-toc` | any | Headings inside are left out of the contents box. |
| `callout callout-note` (… `tip`, `important`, `warning`, `caution`) | `div` | Generated by `> [!NOTE]` and friends. You can write them by hand too. |

All Markdown tables get the classic "wikitable" look: borders, padding, shaded header cells. Wide tables scroll sideways inside their own box on small screens instead of stretching the page.

## Interface structure

For bigger changes, these are the main parts of the page. All class names start with `sp-`:

```text
.sp-header        site name/logo (.sp-brand), search (.sp-search), gear (.sp-gear)
.sp-layout
  nav.sp-sidebar  rendered _Sidebar.md (#sp-sidebar-content)
  .sp-column
    main.sp-main
      article.sp-article
        .sp-content     the rendered page
        .sp-page-info   "last edited" and "view source"
    footer.sp-footer    rendered _Footer.md
dialog.sp-dialog  settings panel
```

## Recipes

**Change the accent color everywhere:**

```css
:root, :root[data-theme="dark"] { --sp-accent: #0b7a75; --sp-link: #0b7a75; --sp-focus: #0b7a75; }
:root[data-theme="dark"] { --sp-link: #5fd3cc; }
```

**Use a self-hosted font:**

```css
@font-face {
  font-family: "Source Serif";
  src: url("fonts/SourceSerif4-Regular.woff2") format("woff2");
  font-display: swap;
}
:root { --sp-font-heading: "Source Serif", Georgia, serif; }
```

**A sepia light theme:**

```css
:root[data-theme="light"] {
  --sp-bg: #f1e7d0; --sp-surface: #fbf4e4; --sp-surface-2: #f4ead3; --sp-surface-3: #e9dcbd;
  --sp-text: #3b2f22; --sp-border-light: #d8c7a3; --sp-code-bg: #efe3c8;
}
```

**Hide the "View source" link with CSS** (or set `showSourceLink` to `false`):

```css
.sp-page-info a[rel="nofollow"] { display: none; }
```

The `style.css` of this manual is a small working example. [View it](style.css).
