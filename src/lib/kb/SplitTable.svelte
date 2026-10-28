<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  // The desk's view (causes.ts): parser, hint and the one-day moves (desk-notes.json), each counted once.
  type Row = { problem: string; what: string; ticket: number; status: string };
  let { ticket, repo, rows }: { ticket: number; repo: string; rows: Row[] } = $props();

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  const groups = $derived(groupByCause(merged, tenantOf, notes.oneDayMove));
  const shift = $derived(groups.find((g) => g.cause === 'shift')?.reports ?? []);
  const count = (p: string) => groups.find((g) => g.cause === p)?.reports.length ?? 0;
  const placed = $derived(groups.reduce((n, g) => n + g.reports.length, 0));
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
  <code>shift</code>: {shift.map((r) => r.id).join(', ')} · {placed === merged.length ? 'every report counted once' : `${merged.length - placed} not placed`}
</p>

<style>
  .sum { font-size: 1.1rem; }
  .ids { color: var(--muted); font-size: 0.85em; }
  .open td { background: var(--warn-bg); }
</style>
