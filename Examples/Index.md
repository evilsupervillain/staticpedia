# Examples

These pages show what Static-Pedia renders and how the files are laid out. They live in the `Examples/` folder of the repository, and this page is `Examples/Index.md`, which is what a link to `Examples/` opens.

<p class="hatnote">The people described here are fictional. They exist only to fill sample articles.</p>

| Page | Demonstrates |
|---|---|
| [John Doe](People/John_Doe) | An encyclopedia article: infobox, hatnote, footnotes, contents box, page-relative images |
| [Jane Doe](People/Jane_Doe) | A second article in the same folder, linked both ways, with a floating figure |
| [People](People/) | A folder index page (`People/Index.md`) |
| [Tables](Tables) | Markdown tables, alignment, wide tables on phones, HTML tables with spans |
| [Images and media](Images_and_Media) | Images, figures, `<picture>`, video, audio, links to files |
| [Markdown showcase](Markdown_Showcase) | Every supported Markdown feature on one page |
| [Wiki links](Wiki_Links) | `[[Page]]` links, labels, anchors, and what a missing page looks like |

## The files behind these pages

```text
Examples/
├── Index.md                 ← this page (?p=Examples/)
├── Tables.md
├── Images_and_Media.md
├── Markdown_Showcase.md
├── Wiki_Links.md
├── files/
│   └── elements.csv         ← linked from Images_and_Media.md
├── media/
│   ├── chime.wav
│   ├── test-pattern.mp4
│   ├── test-pattern.webm
│   └── test-pattern-poster.jpg
└── People/
    ├── Index.md             ← ?p=Examples/People/
    ├── John_Doe.md
    ├── Jane_Doe.md
    └── images/
        ├── john-doe.svg     ← written as images/john-doe.svg inside John_Doe.md
        └── jane-doe.svg
```

Each page links to its neighbours with plain relative links like `[Tables](Tables)` and `[John Doe](People/John_Doe)`, and back up with `../Index.md`. Open any page and use **View source** at the bottom to compare the Markdown with the result.
