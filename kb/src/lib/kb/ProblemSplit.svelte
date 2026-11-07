<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { causeOf, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  // One row per problem in a merged ticket. A report's problem is its cause (causes.ts), except the
  // reports the desk noted as a one-day move: no month-first parse moves a date by one day.
  let { ticket, issues }: { ticket: number; issues: Record<string, string> } = $props();

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  const problemOf = (r: Report) =>
    notes.oneDayMove.includes(r.id) ? 'one-day move' : (causeOf(r, tenantOf(r)) ?? 'unplaced');
  const rows = $derived(
    Object.entries(
      merged.reduce<Record<string, number>>((n, r) => ({ ...n, [problemOf(r)]: (n[problemOf(r)] ?? 0) + 1 }), {}),
    ).sort((a, b) => b[1] - a[1]),
  );
</script>

<p class="sum">
  <strong>{merged.length}</strong> reports merged into #{ticket} ·
  <strong>{rows.length}</strong> {rows.length === 1 ? 'problem' : 'problems'}
</p>

<table>
  <thead><tr><th>problem</th><th>reports</th><th>ticket</th></tr></thead>
  <tbody>
    {#each rows as [problem, n] (problem)}
      <tr>
        <td><code>{problem}</code></td>
        <td>{n}</td>
        <td>{#if issues[problem]}<a href={issues[problem]}>{issues[problem].replace(/^https:\/\/github.com\//, '')}</a>{:else}<span class="none">none yet</span>{/if}</td>
      </tr>
    {/each}
  </tbody>
</table>

<style>
  .sum { font-size: 1.1rem; }
  .none { color: var(--warn); font-weight: 600; }
</style>
