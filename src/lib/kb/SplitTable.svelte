<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, readAs, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  // The desk's own view (causes.ts, as exported), plus the one-day moves it footnotes (desk-notes.json).
  type Row = { problem: string; what: string; ticket: number; status: string };
  let { ticket, repo, rows }: { ticket: number; repo: string; rows: Row[] } = $props();

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  const groups = $derived(groupByCause(merged, tenantOf));
  const shift = $derived(merged.filter((r) => notes.oneDayMove.includes(r.id)));
  const count = (p: string) => (p === 'shift' ? shift.length : (groups.find((g) => g.cause === p)?.reports.length ?? 0));
  const inside = (p: string) => (p === 'shift' ? groups.filter((g) => readAs(g, notes.oneDayMove)).map((g) => g.cause) : []);
</script>

<p class="sum">
  <strong>{merged.length}</strong> unique reports merged into #{ticket} · <strong>{rows.length}</strong> problems
</p>

<table>
  <thead><tr><th>problem</th><th>what goes wrong</th><th>reports</th><th>ticket</th><th>status</th></tr></thead>
  <tbody>
    {#each rows as r (r.problem)}
      <tr class:open={r.status.startsWith('open')}>
        <td><code>{r.problem}</code></td>
        <td>{r.what}</td>
        <td><strong>{count(r.problem)}</strong></td>
        <td><a href="https://github.com/{repo}/issues/{r.ticket}">{repo}#{r.ticket}</a></td>
        <td>{r.status}</td>
      </tr>
    {/each}
  </tbody>
</table>

<p class="ids">
  <code>shift</code>: {shift.map((r) => r.id).join(', ')}. The desk counts these {shift.length} under
  {#each inside('shift') as c (c)}<code>{c}</code>{/each} by tenant, so they are inside that number too.
</p>

<style>
  .sum { font-size: 1.1rem; }
  .ids { color: var(--muted); font-size: 0.85em; }
  .open td { background: var(--warn-bg); }
</style>
