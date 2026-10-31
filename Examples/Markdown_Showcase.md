---
title: Markdown showcase
---

# Markdown showcase

Every Markdown feature Static-Pedia supports, rendered. Use **View source** at the bottom to see the Markdown behind each example.

## Text

Plain paragraphs are separated by blank lines. Inline styles: *italic*, _also italic_, **bold**, __also bold__, ***bold italic***, ~~strikethrough~~, `inline code`, and <kbd>Ctrl</kbd> + <kbd>C</kbd> with HTML. Underscores inside words like snake_case_names stay as they are.

A line ending in two spaces  
or a backslash\
makes a hard line break. Characters with meaning can be escaped: \*not italic\*, \[not a link\], \`not code\`.

HTML entities work: &copy; &mdash; &rarr; &#x2603;. So do inline <mark>highlighted</mark>, <sub>sub</sub>script, <sup>super</sup>script and <abbr title="HyperText Markup Language">HTML</abbr> abbreviations.

## Headings

<div class="no-toc">

# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6

Setext heading
--------------

</div>

(These sample headings sit inside `<div class="no-toc">`, which keeps them out of the contents box.)

Every heading gets an anchor id: this section is [#headings](#headings). Hover over a heading to see its `#` link.

## Links

- Relative page link: [Installation](../Installation)
- With `.md`: [Installation](../Installation.md)
- To a heading on another page: [Wiki links in the manual](../Links_and_Media#wiki-links)
- To a heading on this page: [Footnotes](#footnotes)
- Wiki link: [[Installation]], with a label: [[Examples/Tables|some tables]]
- External: [example.com](https://example.com "A title")
- Autolinks: <https://example.com>, https://www.example.org/path?q=1 and www.example.net
- E-mail: <someone@example.com>
- Reference style: [the CommonMark spec][cm] and [GFM]

[cm]: https://spec.commonmark.org/ "CommonMark"
[GFM]: https://github.github.com/gfm/

## Lists

- Bullets with `-`, `*` or `+`
- Nested by indenting
  - Second level
    - Third level
- Back to the first level

1. Numbered
2. Lists
   1. Nested numbers
   2. Work too
3. Third

5. A list can start at any number
6. Like this one

- A *loose* list has blank lines between items.

- Each item is then a paragraph,

  and an item can hold several paragraphs, code and more:

  ```text
  code inside a list item
  ```

### Task lists

- [x] Write `index.html`
- [x] Write the manual
- [ ] Take over the world

## Quotes and callouts

> Block quotes start with `>`.
>
> > They can be nested,
>
> and contain **Markdown**, lists and code.

> [!NOTE]
> GitHub-style callouts: `> [!NOTE]`, `> [!TIP]`, `> [!IMPORTANT]`, `> [!WARNING]` and `> [!CAUTION]`.

> [!TIP]
> Helpful advice.

> [!IMPORTANT]
> Key information.

> [!WARNING]
> Urgent information.

> [!CAUTION]
> Risks and negative outcomes.

## Code

Fenced code blocks with a language name get a `language-…` class, ready for a highlighter added in `style.css` or a site script:

```js
// index.html renders this page
function greet(name) {
  return `Hello, ${name}!`;
}
```

~~~python
def greet(name):
    return f"Hello, {name}!"
~~~

    Indented by four spaces: also a code block.

## Tables

| Left | Center | Right |
|:-----|:------:|------:|
| a    | b      | c     |
| `1`  | **2**  | 3     |

More on the [Tables](Tables) page.

## Horizontal rules

Three dashes, asterisks or underscores:

---

## Images

![The Static-Pedia logo](../images/logo.svg)

More on [Images and media](Images_and_Media).

## Collapsible sections

<details>
<summary>Click to expand</summary>

Hidden content, with **Markdown** inside because of the blank lines around it.

</details>

## Definition lists (HTML)

<dl>
<dt>Static</dt>
<dd>Served as plain files, with no code running on the server.</dd>
<dt>Pedia</dt>
<dd>From <em>encyclopedia</em>.</dd>
</dl>

## Footnotes

Footnotes are written like this.[^1] They can have any label,[^note] are numbered in the order they are used, and are collected at the bottom of the page with links back.

[^1]: The first footnote.
[^note]: A footnote with a word label.
    Indented lines continue it.

## Sanitized HTML

The paragraph below contains a `<script>`, an `onclick` handler and a `javascript:` link. All three were removed before the page was shown, so nothing happens:

<p onclick="alert('not allowed')">Click me: nothing happens. <a href="javascript:alert('nope')">This link goes nowhere.</a><script>alert('never runs')</script></p>
