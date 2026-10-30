# FAQ and limitations

## Troubleshooting

### Why does opening `index.html` from disk not work?

Browsers don't let a page opened from a `file://` address read other files with `fetch()`. That is a security rule, and Static-Pedia needs to fetch your `.md` files. Static-Pedia detects this case and shows instructions. Run any local web server in the wiki folder instead:

```sh
python3 -m http.server 8000
```

and open <http://localhost:8000/>. [Installation](Installation#previewing-on-your-own-computer) lists other one-line servers.

### Every page says "Page not found"

- Check the file name, including **upper/lower case**. Most servers treat `People.md` and `people.md` as different files.
- On **GitHub Pages**, add an empty `.nojekyll` file. Without it, Markdown files with front matter are converted to HTML and are no longer available as `.md`. See [GitHub Pages](GitHub_Pages#why-nojekyll-matters).
- Make sure the address ends in `/` when the wiki lives in a folder (`/wiki/`, not `/wiki`). See [the trailing-slash note](Installation#installing-under-a-sub-path).

### The sidebar or footer is missing on GitHub Pages

Same cause: Jekyll doesn't publish files starting with `_`. Add `.nojekyll`.

### My images or links are broken

Relative paths are resolved from the folder of the page they appear in, not from the wiki root. In `People/John_Doe.md`, `images/a.png` means `People/images/a.png`. Use `../images/a.png` or `/images/a.png` (where a leading `/` means the wiki root) for files at the root. See [Links and media](Links_and_Media).

### A link to `Version_2.0` opens a file instead of a page

Links with a file extension are treated as files. `.0` has no letters, so `Version_2.0` is still a page, but `Release.notes` would be a file. Add `.md` to be explicit: `[notes](Release.notes.md)`.

### I changed a page but still see the old version

Static-Pedia asks the server to revalidate each page (`cache: no-cache`), so edits normally show up on the next click. A CDN in front of your host may still cache files for a while. `style.css` and `staticpedia.json` follow your host's normal caching rules; a hard refresh (<kbd>Ctrl</kbd>/<kbd>Cmd</kbd> + <kbd>Shift</kbd> + <kbd>R</kbd>) helps.

### Search doesn't find my new page

Regenerate `pages.json` with `node tools/build-index.mjs`. See [Search](Search#keeping-the-index-up-to-date).

## Limitations

These are deliberate. They keep Static-Pedia a single file that runs on any static host.

- **No editing in the browser.** Static-Pedia is read-only. Edit the Markdown files with any editor and upload them, or use GitHub's web editor and pull requests.
- **No server-side rendering.** Pages are built in the reader's browser with JavaScript. Without JavaScript, readers see a short notice. Search engines that run JavaScript, like Google, can index the pages; ones that don't only see the empty shell.
- **No `file://`.** A web server is always needed, even locally.
- **No automatic page list.** Static hosts don't provide directory listings, so Static-Pedia can't discover pages by itself. Navigation comes from your links, `_Sidebar.md`, and the optional `pages.json`.
- **No red links by default.** Static-Pedia can't cheaply check whether a linked page exists, so links to missing pages look like normal links and lead to the "page not found" page. You can mark them by hand with `class="new"`.
- **Fixed home page and addresses.** The home page is always `Index.md`, and pages always use `?p=`. Pretty URLs like `/wiki/People/John_Doe` would need server rewrites.
- **Simple front matter.** Only one-line `key: value` pairs are read.
- **Markdown inside HTML blocks** is only processed when separated from the tags by blank lines, as in CommonMark.
- **Embedded content** (`<iframe>`) is blocked unless its host is allowed in [`iframeHosts`](Configuration). Inline `<svg>` and `<script>` are always removed. Use `<img src="diagram.svg">` for SVG images.

## Other questions

### Is it safe to publish pages written by other people?

Yes. All HTML, including what Markdown produces, goes through a built-in allowlist sanitizer that removes scripts, event handlers, `javascript:` URLs, forms and embeds ([details](Writing_Pages#what-the-sanitizer-removes)). Authors can still use inline `style` attributes and CSS classes, so they can change how their own page looks.

### Does it work offline or in an air-gapped network?

Yes. `index.html` has no external dependencies: no CDN scripts, no web fonts, no analytics. As long as the `.md` files are served from the same place, the wiki works without internet access. Only things you embed yourself (external images, allowed iframes) need the network.

### Which browsers are supported?

Current versions of Chrome, Edge, Firefox and Safari on desktop and mobile. Static-Pedia uses ES2020 JavaScript, the `<dialog>` element and CSS custom properties. There is no support for Internet Explorer.

### How large can a wiki be?

Pages are loaded one at a time, so the number of pages doesn't matter. The search index is downloaded in full on each visit, so for very large wikis keep only a short excerpt of each page with `--max-chars 300`.

### Can I have several wikis on one site?

Yes. Each folder with its own `index.html` is an independent wiki with its own `Index.md`, sidebar, style and config. They share the reader's [settings](Settings#how-settings-are-stored) if they are on the same origin.

### Can I print a page?

Yes. The print stylesheet hides the header, sidebar and buttons and prints just the article.

### Does it use cookies or track readers?

No. The only thing stored is the reader's settings, in their own browser's `localStorage`, and it is never sent anywhere.
