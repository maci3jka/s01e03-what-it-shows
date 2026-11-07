<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { causeOf, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  // One row per problem in a merged ticket, counted the desk's way (as CauseTable does): each report
  // under its cause (causes.ts), plus the reports the desk noted as a one-day move. Those stay counted
  // under their cause too, so the rows overlap by that many.
  let { ticket, issues }: { ticket: number; issues: Record<string, string> } = $props();

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  const moved = $derived(merged.filter((r) => notes.oneDayMove.includes(r.id)).length);
  const rows = $derived([
    ...Object.entries(
      merged.reduce<Record<string, number>>((n, r) => {
        const c = causeOf(r, tenantOf(r)) ?? 'unplaced';
        return { ...n, [c]: (n[c] ?? 0) + 1 };
      }, {}),
    ).sort((a, b) => b[1] - a[1]),
    ...(moved ? [['one-day move', moved] as [string, number]] : []),
  ]);
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
{#if moved}
  <p class="note">The {moved} one-day moves are also counted under the cause their tenant files them under.</p>
{/if}

<style>
  .sum { font-size: 1.1rem; }
  .none { color: var(--warn); font-weight: 600; }
  .note { color: var(--muted); font-size: 0.85em; }
</style>
