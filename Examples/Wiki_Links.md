# Wiki links

Double square brackets link to a page by name, resolved from the **wiki root** no matter which folder the current page is in. Spaces become underscores.

| Written | Rendered |
|---|---|
| `[[Installation]]` | [[Installation]] |
| `[[Writing Pages]]` | [[Writing Pages]] |
| `[[Examples/People/John Doe]]` | [[Examples/People/John Doe]] |
| `[[Examples/People/Jane_Doe\|Jane]]` | [[Examples/People/Jane_Doe\|Jane]] |
| `[[Links and Media#Wiki links]]` | [[Links and Media#Wiki links]] |
| `[[#Missing pages]]` | [[#Missing pages]] |

Wiki links also work in running text: read [[FAQ]] or go back to [[Index|the main page]].

Compare with a **relative** Markdown link, which is resolved from this page's folder (`Examples/`): [Tables](Tables) goes to `Examples/Tables.md`, while `[[Tables]]` would look for `Tables.md` at the root.

## Missing pages

Links to pages that don't exist look like normal links. Following one shows a friendly "page not found" page with a way back:

- [[This Page Does Not Exist]]
- [A broken relative link](Nowhere/Nothing)

To flag a link as pointing to a page that hasn't been written yet, give it the class `new` in HTML. It turns red: <a class="new" href="Not_Written_Yet">Not written yet</a>.
