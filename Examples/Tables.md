# Tables

Markdown tables use the GitHub syntax. Every table gets the classic encyclopedia "wikitable" style from the built-in stylesheet.

## A simple table

```markdown
| Element  | Symbol | Atomic number |
|----------|:------:|--------------:|
| Hydrogen | H      | 1             |
| Helium   | He     | 2             |
| Lithium  | Li     | 3             |
```

| Element  | Symbol | Atomic number |
|----------|:------:|--------------:|
| Hydrogen | H      | 1             |
| Helium   | He     | 2             |
| Lithium  | Li     | 3             |

The colons in the second row set alignment: `:---` left, `:---:` center, `---:` right. Cells can contain inline Markdown: **bold**, `code`, [links](../Writing_Pages) and so on. Write a literal pipe as `\|`.

| Syntax | Result |
|---|---|
| `**bold**` | **bold** |
| `a \| b` | a \| b |
| `[[Index]]` | [[Index]] |

## A wide table

On a phone this table scrolls sideways inside its own box instead of stretching the page.

| Year | Jan | Feb | Mar | Apr | May | Jun | Jul | Aug | Sep | Oct | Nov | Dec | Total |
|---|--:|--:|--:|--:|--:|--:|--:|--:|--:|--:|--:|--:|--:|
| 2022 | 120 | 98 | 143 | 151 | 166 | 172 | 190 | 185 | 160 | 149 | 130 | 128 | 1792 |
| 2023 | 131 | 105 | 150 | 162 | 170 | 181 | 199 | 193 | 171 | 155 | 137 | 140 | 1894 |
| 2024 | 140 | 118 | 158 | 170 | 182 | 190 | 210 | 201 | 180 | 166 | 150 | 149 | 2014 |

## HTML tables

For merged cells, captions or header columns, write the table in HTML:

<table>
<caption>Survey results by region</caption>
<thead>
<tr><th rowspan="2">Region</th><th colspan="2">Coastline (km)</th></tr>
<tr><th>Surveyed</th><th>Invented</th></tr>
</thead>
<tbody>
<tr><th scope="row">North</th><td>1,204</td><td>310</td></tr>
<tr><th scope="row">South</th><td>988</td><td>1,422</td></tr>
<tr><td colspan="3" style="text-align:center"><em>Figures are fictional.</em></td></tr>
</tbody>
</table>

```html
<table>
<caption>Survey results by region</caption>
<thead>
<tr><th rowspan="2">Region</th><th colspan="2">Coastline (km)</th></tr>
<tr><th>Surveyed</th><th>Invented</th></tr>
</thead>
...
</table>
```

## A table without borders

Add `class="plain"` to a table to drop the default borders and background, which is handy for layout:

<table class="plain">
<tr><td><img src="../images/logo.svg" alt="" width="48"></td><td><strong>Static-Pedia</strong><br>One file. Any host.</td></tr>
</table>

## Data files

The same data as a downloadable file: [elements.csv](files/elements.csv).
