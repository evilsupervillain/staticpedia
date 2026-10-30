# Settings panel

The **gear button** in the upper-right corner of every page opens a settings panel. Readers use it to adjust the wiki to their taste. Choices take effect immediately and are remembered in their browser.

## The settings

| Setting | Choices | Default |
|---|---|---|
| **Theme** | System, Light, Dark | System: follows the operating system and switches when it does |
| **Text size** | Small, Standard, Large, Extra large | Standard |
| **Content width** | Narrow (a comfortable reading measure), Wide (use the whole window) | Narrow |
| **Show table of contents** | on / off | on |
| **Show sidebar** | on / off | on |

**Reset to defaults** forgets the reader's choices and goes back to the site's defaults. The site owner can change those defaults with `defaultTheme` and `defaults` in [`staticpedia.json`](Configuration).

On wide screens the ☰ button next to the site name toggles **Show sidebar** too. On phones the sidebar is always a slide-out drawer opened with ☰, whatever the setting says.

## Keyboard and accessibility

- The panel is a modal dialog. Focus moves into it when it opens, <kbd>Tab</kbd> cycles through its controls without escaping to the page behind, and focus returns to the gear button when it closes.
- <kbd>Esc</kbd>, the **Done** button, the × button or a click on the dimmed backdrop close it.
- Theme, text size and width are radio groups (arrow keys move between choices). The other two are checkboxes.
- Elsewhere on the page, <kbd>/</kbd> jumps to the search box and <kbd>Esc</kbd> closes the mobile sidebar.
- Animations are turned off when the reader's system asks for reduced motion.

## How settings are stored

Settings live in `localStorage` under a single key, **`staticpedia:settings`**, as a small JSON object. Only the settings a reader actually changed are saved, so site defaults still apply to the rest:

```json
{
  "theme": "dark",
  "fontSize": "large",
  "_site": { "theme": "system", "fontSize": "medium", "width": "narrow", "toc": true, "sidebar": true }
}
```

`_site` is a cached copy of the site's defaults from `staticpedia.json`. It lets the theme be applied **before the page is first drawn**, so readers never see a flash of the wrong theme.

All storage access is wrapped so that a browser blocking storage (private modes, strict privacy settings, sandboxed iframes) never breaks the wiki. Settings then still work but last only until the reader leaves the page, and the panel says so.

Every Static-Pedia wiki on the same origin (for example several wikis under one `https://user.github.io/` address) shares the same key, so a reader's preferences follow them between those wikis. Changing a setting in one tab updates other open tabs immediately.

## Adding your own setting

The settings are defined by one schema near the top of `index.html`, and the panel is generated from it. To add a setting, for example a "Justify text" toggle:

1. Find `const SCHEMA = [` in the first `<script>` in `index.html` and add an entry:

   ```js
   { key: 'justify', label: 'Justify text', type: 'toggle', default: false },
   ```

   A choice setting looks like this:

   ```js
   { key: 'font', label: 'Font', type: 'choice', default: 'sans',
     options: [['sans', 'Sans-serif'], ['serif', 'Serif']] },
   ```

2. Each setting is written to `<html>` as a `data-*` attribute (`justify` → `data-justify="on"`, `fontSize` → `data-font-size="large"`), before the first paint. Style it in `style.css`:

   ```css
   :root[data-justify="on"] .sp-content p { text-align: justify; hyphens: auto; }
   :root[data-font="serif"] { --sp-font-body: Georgia, serif; }
   ```

Site owners can then set a default with `"defaults": { "justify": true }` in `staticpedia.json`.

Scripts can also read and change settings through `window.StaticPedia.settings.get('theme')`, `.set('theme', 'dark')` and `.open()`.
