<script lang="ts">
import BreedBars from "./BreedBars.svelte";

type Breed = { name: string; pct: number; color: string };
type Dog = {
	name: string;
	birthday: Date;
	image: string;
	breeds: Breed[];
	heroPos: string;
};

const dogs: Dog[] = [
	{
		name: "Echo",
		birthday: new Date(2022, 10, 6),
		image: "/images/dogs/Echo.png",
		breeds: [{ name: "German Shepherd", pct: 100, color: "#6B8E6B" }],
		heroPos: "50% 38%",
	},
	{
		name: "River",
		birthday: new Date(2021, 4, 9),
		image: "/images/dogs/River.png",
		breeds: [
			{ name: "Golden Retriever", pct: 33.37, color: "#E9B949" },
			{ name: "Doberman Pinscher", pct: 18.1, color: "#5B89D6" },
			{ name: "Rottweiler", pct: 16.2, color: "#D85B63" },
			{ name: "Chow Chow", pct: 16, color: "#9D79C7" },
			{ name: "Supermutt", pct: 10.4, color: "#55AD8B" },
			{ name: "Cocker Spaniel", pct: 5.93, color: "#EC9A65" },
		],
		heroPos: "50% 42%",
	},
	{
		name: "Kyle",
		birthday: new Date(2025, 10, 15),
		image: "/images/dogs/Kyle.png",
		breeds: [
			{ name: "Pomeranian", pct: 28, color: "#F1B81A" },
			{ name: "Chihuahua", pct: 22, color: "#3AA6E0" },
			{ name: "Poodle (Small)", pct: 20.3, color: "#E85454" },
			{ name: "Pekingese", pct: 18.6, color: "#9D6EE8" },
			{ name: "Lhasa Apso", pct: 11.1, color: "#EABF86" },
		],
		heroPos: "50% 20%",
	},
];

let selected = "Kyle";
$: active = dogs.find((d) => d.name === selected) ?? dogs[2];

function getAge(birthday: Date): string {
	const today = new Date();
	let years = today.getFullYear() - birthday.getFullYear();
	let months = today.getMonth() - birthday.getMonth();
	if (today.getDate() < birthday.getDate()) months -= 1;
	if (months < 0) {
		years -= 1;
		months += 12;
	}
	if (years <= 0) return `${months} month${months === 1 ? "" : "s"} old`;
	if (months === 0) return `${years} year${years === 1 ? "" : "s"} old`;
	return `${years} yr ${months} mo old`;
}

const birthdayFormatter = new Intl.DateTimeFormat("en-US", {
	month: "long",
	day: "numeric",
	year: "numeric",
});
</script>

<section class="dogs-section" aria-labelledby="dogs-heading">
  <div class="dogs-head">
    <h2 id="dogs-heading">Dogs</h2>
    <p class="dogs-sub">Meet our dogs</p>
  </div>

  <div class="dog-tabs" role="tablist" aria-label="Dog profiles">
    {#each dogs as dog}
      <button
        type="button"
        role="tab"
        aria-selected={selected === dog.name}
        class:active={selected === dog.name}
        on:click={() => (selected = dog.name)}
      >
        <img src={dog.image} alt="" aria-hidden="true" loading="lazy" style:object-position={dog.heroPos} />
        <span class="tab-meta">
          <span class="tab-name">{dog.name}</span>
          <span class="tab-detail">{dog.breeds.length === 1 ? "Purebred" : `${dog.breeds.length} breeds`} · {getAge(dog.birthday)}</span>
        </span>
      </button>
    {/each}
  </div>

  <div class="dog-card" role="tabpanel" aria-label="{active.name} profile">
    <div class="hero">
      <img src={active.image} alt={active.name} loading="eager" style:object-position={active.heroPos} />
      <div class="hero-gradient" aria-hidden="true"></div>
      <div class="hero-text">
        <h3>{active.name}</h3>
        <div class="hero-badges">
          <span class="badge age">{getAge(active.birthday)}</span>
          <span class="badge born">Born {birthdayFormatter.format(active.birthday)}</span>
        </div>
      </div>
    </div>

    <div class="card-body">
      <div class="embark-kicker">
        <span class="kicker-dot" aria-hidden="true"></span>
        Embark · Breed Mix
      </div>
      <BreedBars breeds={active.breeds} />
      <p class="embark-footnote">
        {#if active.breeds.length === 1}
          Purebred result — Embark DNA.
        {:else}
          {active.name}'s Embark results — {active.breeds.length} breeds as tested.
        {/if}
      </p>
    </div>
  </div>
</section>

<style>
  .dogs-section { margin-top: 2.2rem; }
  .dogs-head { margin-bottom: 0.9rem; }
  h2 {
    color: #111827;
    font-size: 1.5rem; font-weight: 800; margin: 0; letter-spacing: -0.02em;
  }
  :global(html.dark) h2 { color: #f3f4f6; }
  .dogs-sub { margin: 0.15rem 0 0; font-size: 0.9rem; color: #6b7280; }
  :global(html.dark) .dogs-sub { color: rgba(255,255,255,0.62); }

  .dog-tabs {
    display: grid; grid-template-columns: repeat(3, minmax(0,1fr));
    gap: 0.6rem; margin-bottom: 1rem;
  }
  @media (max-width: 640px) { .dog-tabs { grid-template-columns: 1fr; } }
  .dog-tabs button {
    display: flex; align-items: center; gap: 0.7rem;
    border: 1px solid var(--line-divider, rgba(0,0,0,0.08));
    border-radius: 1rem; background: var(--card-bg, white);
    padding: 0.55rem 0.7rem; cursor: pointer; text-align: left;
    transition: border-color 0.18s, box-shadow 0.18s, background 0.18s, transform 0.12s;
    box-shadow: 0 1px 0 rgba(0,0,0,0.03);
  }
  :global(html.dark) .dog-tabs button { background: rgba(255,255,255,0.06); border-color: rgba(255,255,255,0.08); }
  .dog-tabs button:hover { border-color: var(--primary); transform: translateY(-1px); }
  .dog-tabs button.active {
    border-color: var(--primary);
    background: var(--btn-regular-bg, #f5f5f5);
    box-shadow: 0 4px 18px rgba(0,0,0,0.07);
  }
  :global(html.dark) .dog-tabs button.active { background: rgba(255,255,255,0.10); }
  .dog-tabs button img {
    width: 2.6rem; height: 2.6rem; border-radius: 50%; object-fit: cover; flex-shrink: 0; border: 2px solid white; box-shadow: 0 1px 6px rgba(0,0,0,0.12);
  }
  .tab-meta { display: flex; flex-direction: column; min-width: 0; }
  .tab-name { font-weight: 700; font-size: 0.98rem; line-height: 1.1; color: #111827; }
  :global(html.dark) .tab-name { color: #f3f4f6; }
  .tab-detail { font-size: 0.76rem; color: #6b7280; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  :global(html.dark) .tab-detail { color: rgba(255,255,255,0.58); }

  .dog-card {
    border: 1px solid var(--line-divider, rgba(0,0,0,0.08));
    border-radius: var(--radius-large, 1rem);
    overflow: hidden; background: var(--card-bg, white);
    box-shadow: 0 8px 28px rgba(0,0,0,0.06);
  }
  :global(html.dark) .dog-card { background: #1f242e; border-color: rgba(255,255,255,0.08); }

  .hero { position: relative; aspect-ratio: 16 / 10; max-height: 22rem; overflow: hidden; background: #111; }
  @media (max-width: 640px){ .hero{ aspect-ratio: 4 / 3; max-height: none; } }
  .hero img { width: 100%; height: 100%; object-fit: cover; display: block; }
  .hero-gradient {
    position: absolute; inset: 0;
    background: linear-gradient(to top, rgba(0,0,0,0.72) 0%, rgba(0,0,0,0.28) 42%, rgba(0,0,0,0.0) 72%);
  }
  .hero-text {
    position: absolute; left: 1.15rem; right: 1.15rem; bottom: 1rem; color: white;
  }
  .hero-text h3 { margin: 0; font-size: 1.85rem; font-weight: 800; letter-spacing: -0.02em; text-shadow: 0 1px 10px rgba(0,0,0,0.35); }
  .hero-badges { display: flex; flex-wrap: wrap; gap: 0.4rem; margin-top: 0.5rem; }
  .badge {
    font-size: 0.75rem; font-weight: 600; letter-spacing: 0.02em;
    background: rgba(255,255,255,0.92); color: #111; border-radius: 999px; padding: 0.28rem 0.62rem; backdrop-filter: blur(6px);
  }
  .badge.born { background: rgba(255,255,255,0.18); color: white; border: 1px solid rgba(255,255,255,0.28); }

  .card-body { padding: 1.15rem 1.15rem 1.05rem; color: #1f2937; }
  :global(html.dark) .card-body { color: rgba(255,255,255,0.92); }
  .embark-kicker {
    display: flex; align-items: center; gap: 0.45rem;
    font-size: 0.7rem; font-weight: 700; letter-spacing: 0.14em; text-transform: uppercase; color: #6b7280; margin-bottom: 0.32rem;
  }
  :global(html.dark) .embark-kicker { color: rgba(255,255,255,0.52); }
  .kicker-dot { width: 0.55rem; height: 0.55rem; border-radius: 50%; background: var(--primary); display: inline-block; }
  .embark-headline { margin: 0 0 1rem; font-size: 1.08rem; font-weight: 700; letter-spacing: -0.015em; color: #111827; }
  :global(html.dark) .embark-headline { color: #f3f4f6; }
  .embark-footnote { margin: 0.9rem 0 0; font-size: 0.78rem; color: #6b7280; }
  :global(html.dark) .embark-footnote { color: rgba(255,255,255,0.48); }
</style>


