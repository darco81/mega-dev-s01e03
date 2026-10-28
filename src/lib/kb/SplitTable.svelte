<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';
  import { splitTicket, type Problem } from '#lib/kb/split';

  // tickets: problem → its own ticket (GitHub issue number in `repo`).
  let { ticket, repo, tickets, what }: { ticket: number; repo: string; tickets: Partial<Record<Problem, number>>; what: Partial<Record<Problem, string>> } = $props();

  const split = $derived(splitTicket(reports as Report[], ticket, tenantOf, notes.oneDayMove));
  const sum = $derived(split.groups.reduce((n, g) => n + g.ids.length, 0));
</script>

<p class="sum">
  <strong>{split.total}</strong> unique reports merged into #{ticket} ·
  <strong>{split.groups.length}</strong> problems · {sum === split.total ? 'every report placed once' : `${split.total - sum} not placed`}
</p>

<table>
  <thead><tr><th>problem</th><th>what goes wrong</th><th>reports</th><th>tenants</th><th>seen</th><th>ticket</th></tr></thead>
  <tbody>
    {#each split.groups as g (g.problem)}
      <tr>
        <td><code>{g.problem}</code></td>
        <td>{what[g.problem] ?? ''}</td>
        <td><strong>{g.ids.length}</strong></td>
        <td>{g.tenants}</td>
        <td>{g.firstSeen} → {g.lastSeen}</td>
        <td>
          {#if tickets[g.problem]}<a href="https://github.com/{repo}/issues/{tickets[g.problem]}">{repo}#{tickets[g.problem]}</a>{:else}none yet{/if}
        </td>
      </tr>
    {/each}
  </tbody>
</table>

{#each split.groups as g (g.problem)}
  {#if g.ids.length < 50}
    <p class="ids"><code>{g.problem}</code>: {g.ids.join(', ')}</p>
  {/if}
{/each}

<style>
  .sum { font-size: 1.1rem; }
  .ids { color: var(--muted); font-size: 0.85em; }
</style>
