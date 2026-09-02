<script lang="ts">
export let breeds: { name: string; pct: number; color: string }[] = [];
</script>

<div class="breed-bars" aria-label="Breed composition">
  <div class="bar-track" role="img" aria-label="Breed percentages">
    {#each breeds as b, i}
      <div class="bar-seg" style="width: {b.pct}%; background: {b.color}; border-left: {i===0 ? 0 : 2}px solid var(--card-bg, #1f242e)" title="{b.name} {b.pct}%"></div>
    {/each}
  </div>
  <ul class="breed-legend">
    {#each breeds as b}
      <li>
        <span class="dot" style="background:{b.color}"></span>
        <span class="name">{b.name}</span>
        <span class="pct">{b.pct}%</span>
      </li>
    {/each}
  </ul>
</div>

<style>
  .breed-bars { display:flex; flex-direction:column; gap:0.85rem; }
  .bar-track { display:flex; height:16px; border-radius:999px; overflow:hidden; background:rgba(0,0,0,0.08); border:1px solid rgba(0,0,0,0.06); }
  :global(html.dark) .bar-track { background:rgba(255,255,255,0.10); border-color:rgba(255,255,255,0.08); }
  .bar-seg { height:100%; flex-shrink:0; }
  .breed-legend {
    list-style:none; margin:0; padding:0;
    display:flex; flex-wrap:wrap; gap:0.55rem 1.1rem;
  }
  .breed-legend li {
    display:flex; align-items:center; gap:0.5rem;
    font-size:0.88rem; line-height:1.2;
    flex: 1 1 160px; max-width: 220px;
    min-width: 140px;
    background: rgba(0,0,0,0.03);
    border-radius: 999px;
    padding: 0.28rem 0.6rem 0.28rem 0.4rem;
    border: 1px solid rgba(0,0,0,0.06);
  }
  :global(html.dark) .breed-legend li {
    background: rgba(255,255,255,0.06);
    border-color: rgba(255,255,255,0.08);
  }
  .dot { width:0.85rem; height:0.85rem; border-radius:50%; flex-shrink:0; border:1.5px solid white; box-shadow:0 0 0 1px rgba(0,0,0,0.12); }
  :global(html.dark) .dot { border-color: rgba(255,255,255,0.9); box-shadow:0 0 0 1px rgba(0,0,0,0.25); }
  .name { flex:1; font-weight:500; color:#1f2937; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
  :global(html.dark) .name { color: rgba(255,255,255,0.92); }
  .pct { font-variant-numeric: tabular-nums; font-weight:700; color:#111827; flex-shrink:0; }
  :global(html.dark) .pct { color: #f9fafb; }
</style>
