# kb — the support knowledge base

**kb** = knowledge base. What the support desk knows about how our products break, written
down where the code can keep it honest.

One desk takes reports for every product we run: `sched-prod` (calendar invites for about
300k people) and the rest. When something
goes wrong, the reports reach the desk first. This kb is how the desk triages them, what each
incident turned out to be, and why we decided what we decided.

## What's here

```
src/routes/kb/      the pages (.svx: Markdown with components)
  triage/           how the desk handles incoming reports
  incidents/        one page per big ticket
  decisions/        numbered decision records
src/lib/kb/         components and the logic they run
src/lib/layouts/    the frame every page shares
src/lib/data/       exports from the ticket system
```

## Conventions

- Words in the page, logic in `src/lib` (imported as `#lib/…`). A number that can be computed belongs in a component.
- Every page starts with frontmatter: `title`, `owner`, `status`, `reviewed`.
- A decision is never edited after it is accepted. A new record replaces it.
- A page you add is signed: its frontmatter carries your `signature` and it ends with `<RenderStamp {signature} />`, so the desk knows who wrote it.

## Owners

Support desk. Decision records: whoever signed them.

---

Exported 2026-10-04 from the `support` repo, `apps/kb/` → `src/`.

---

## Added for #4471 (2026-10-28)

- `src/routes/kb/incidents/4471/split/+page.svx`: the signed page that splits #4471 into its three problems.
- `src/lib/kb/SplitTable.svelte`: the table behind it, built on the desk's own `causes.ts`.
- `src/routes/kb/decisions/0002-dedupe-by-cause/+page.svx`: proposed record to replace 0001.
- `rendered/kb/incidents/4471/split.html`: the page as the desk sees it (`npm install && npm run build`).
- Tickets: issues #1 (parser, 379, fixed), #2 (booking-v3 hint, 33, won't fix), #3 (one-day shift, 5, open).
