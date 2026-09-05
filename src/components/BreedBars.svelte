<script lang="ts">
export let breeds: { name: string; pct: number; color: string }[] = [];
</script>

<div class="breed-bars" aria-label="Breed composition">
  <ul class="breed-list">
    {#each breeds as b}
      <li class="breed-row">
        <div class="breed-head">
          <span class="dot" style="background:{b.color}" aria-hidden="true"></span>
          <span class="name">{b.name}</span>
          <span class="pct">{b.pct}%</span>
        </div>
        <div class="breed-track" aria-hidden="true">
          <div class="breed-fill" style="width: {b.pct}%; background: {b.color}"></div>
        </div>
      </li>
    {/each}
  </ul>
</div>

<style>
  .breed-bars {
    display: flex;
    flex-direction: column;
    gap: 0;
  }

  .breed-list {
    list-style: none;
    margin: 0;
    padding: 0;
    display: flex;
    flex-direction: column;
    gap: 0.65rem;
  }

  .breed-row {
    display: flex;
    flex-direction: column;
    gap: 0.32rem;
  }

  .breed-head {
    display: flex;
    align-items: center;
    gap: 0.55rem;
    min-width: 0;
  }

  .dot {
    width: 0.6rem;
    height: 0.6rem;
    border-radius: 50%;
    flex-shrink: 0;
    border: 1.5px solid white;
    box-shadow: 0 0 0 1px rgba(0, 0, 0, 0.1);
  }
  :global(html.dark) .dot {
    border-color: rgba(255, 255, 255, 0.92);
  }

  .name {
    flex: 1;
    min-width: 0;
    font-size: 0.875rem;
    font-weight: 600;
    line-height: 1.3;
    color: #1f2937;
    overflow-wrap: break-word;
  }
  :global(html.dark) .name {
    color: rgba(255, 255, 255, 0.92);
  }

  .pct {
    flex-shrink: 0;
    font-size: 0.84rem;
    font-weight: 700;
    font-variant-numeric: tabular-nums;
    color: #111827;
    letter-spacing: -0.01em;
  }
  :global(html.dark) .pct {
    color: #f9fafb;
  }

  .breed-track {
    height: 6px;
    border-radius: 999px;
    overflow: hidden;
    background: var(--btn-regular-bg, rgba(0, 0, 0, 0.06));
    border: 1px solid transparent;
  }
  :global(html.dark) .breed-track {
    background: rgba(255, 255, 255, 0.07);
  }

  .breed-fill {
    height: 100%;
    border-radius: 999px;
    transition: width 0.4s ease;
  }
</style>
