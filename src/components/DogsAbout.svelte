<script lang="ts">
import { fade } from "svelte/transition";
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
let tabButtons: HTMLButtonElement[] = [];
$: active = dogs.find((d) => d.name === selected) ?? dogs[2];

function getAgeParts(birthday: Date) {
	const today = new Date();
	let years = today.getFullYear() - birthday.getFullYear();
	let months = today.getMonth() - birthday.getMonth();
	if (today.getDate() < birthday.getDate()) months -= 1;
	if (months < 0) {
		years -= 1;
		months += 12;
	}
	return { years, months };
}

function getAge(birthday: Date): string {
	const { years, months } = getAgeParts(birthday);
	if (years <= 0) return `${months} month${months === 1 ? "" : "s"} old`;
	if (months === 0) return `${years} year${years === 1 ? "" : "s"} old`;
	return `${years} yr ${months} mo old`;
}

function getShortAge(birthday: Date): string {
	const { years, months } = getAgeParts(birthday);
	if (years <= 0) return `${months} mo`;
	return `${years} yr${years === 1 ? "" : "s"}`;
}

function getBreedSummary(dog: Dog): string {
	if (dog.breeds.length === 1) return "Purebred";
	return `${dog.breeds.length}-breed mix`;
}

// Arrow keys move between tabs, matching the WAI-ARIA tabs pattern
function onTabKeydown(event: KeyboardEvent, index: number) {
	const keys: Record<string, number> = {
		ArrowRight: index + 1,
		ArrowDown: index + 1,
		ArrowLeft: index - 1,
		ArrowUp: index - 1,
		Home: 0,
		End: dogs.length - 1,
	};
	if (!(event.key in keys)) return;
	event.preventDefault();
	const next = (keys[event.key] + dogs.length) % dogs.length;
	selected = dogs[next].name;
	tabButtons[next]?.focus();
}

const birthdayFormatter = new Intl.DateTimeFormat("en-US", {
	month: "long",
	day: "numeric",
	year: "numeric",
});
</script>

<section class="dogs-section" aria-labelledby="dogs-heading">
  <div class="dogs-head">
    <h2 id="dogs-heading"><span class="heading-bar" aria-hidden="true"></span> Dogs</h2>
    <p class="dogs-sub text-50">Meet the pack</p>
  </div>

  <div class="dog-tabs" role="tablist" aria-label="Dog profiles">
    {#each dogs as dog, i}
      <button
        type="button"
        role="tab"
        id="dog-tab-{dog.name}"
        aria-controls="dog-panel"
        aria-selected={selected === dog.name}
        tabindex={selected === dog.name ? 0 : -1}
        class:active={selected === dog.name}
        bind:this={tabButtons[i]}
        on:click={() => (selected = dog.name)}
        on:keydown={(e) => onTabKeydown(e, i)}
      >
        <span class="tab-thumb">
          <img src={dog.image} alt="" loading="lazy" style:object-position={dog.heroPos} />
        </span>
        <span class="tab-name text-90">{dog.name}</span>
        <span class="tab-detail text-50">{getShortAge(dog.birthday)}</span>
      </button>
    {/each}
  </div>

  <div class="dog-showcase" id="dog-panel" role="tabpanel" aria-labelledby="dog-tab-{active.name}">
    <div class="photo-wrap">
      {#key active.name}
        <img
          src={active.image}
          alt={active.name}
          style:object-position={active.heroPos}
          in:fade={{ duration: 250 }}
        />
      {/key}
    </div>

    <div class="info-wrap">
      <div class="info-head">
        <h3 class="info-name text-90">{active.name}</h3>
        <div class="info-badges">
          <span class="ibadge age">{getAge(active.birthday)}</span>
          <span class="ibadge born">Born {birthdayFormatter.format(active.birthday)}</span>
        </div>
      </div>

      <div class="dna">
        <div class="dna-label">
          <span class="text-75">{getBreedSummary(active)}</span>
          <span class="text-50">Embark DNA</span>
        </div>
        <div class="dna-bar" role="img" aria-label="Breed composition: {active.breeds.map((b) => `${b.name} ${b.pct}%`).join(', ')}">
          {#each active.breeds as breed (breed.name)}
            <span class="dna-segment" style="flex-grow:{breed.pct}; background:{breed.color}" title="{breed.name} {breed.pct}%"></span>
          {/each}
        </div>
      </div>

      <BreedBars breeds={active.breeds} />
    </div>
  </div>
</section>

<style>
  .dogs-section {
    margin-top: 2.4rem;
    padding-top: 1.6rem;
    border-top: 1px solid var(--line-divider, rgba(0,0,0,0.08));
  }
  .dogs-head { margin-bottom: 1rem; }
  h2 {
    display: flex; align-items: center; gap: 0.75rem;
    font-size: 1.35rem; font-weight: 800; letter-spacing: -0.02em;
    color: #111827; margin: 0;
  }
  :global(html.dark) h2 { color: #f3f4f6; }
  .heading-bar {
    width: 4px; height: 1.15rem; border-radius: 999px;
    background: var(--primary); flex-shrink: 0;
  }
  .dogs-sub {
    margin: 0.35rem 0 0 1.05rem;
    font-size: 0.88rem; line-height: 1.4;
  }

  /* Dog picker */
  .dog-tabs {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 0.6rem;
    margin-bottom: 1rem;
  }
  .dog-tabs button {
    display: flex; flex-direction: column; align-items: center;
    gap: 0.15rem;
    padding: 0.85rem 0.5rem 0.7rem;
    border-radius: 1rem;
    border: 1px solid var(--line-divider, rgba(0,0,0,0.08));
    background: var(--btn-regular-bg);
    cursor: pointer;
    font: inherit;
    transition: border-color 0.18s, background 0.18s, transform 0.15s;
  }
  .dog-tabs button:hover {
    border-color: color-mix(in oklch, var(--primary) 45%, transparent);
    transform: translateY(-2px);
  }
  .dog-tabs button:focus-visible {
    outline: 2px solid var(--primary);
    outline-offset: 2px;
  }
  .dog-tabs button.active {
    background: color-mix(in oklch, var(--primary) 12%, var(--card-bg));
    border-color: var(--primary);
  }
  .tab-thumb {
    width: 3.5rem; height: 3.5rem; border-radius: 50%; overflow: hidden;
    margin-bottom: 0.35rem;
    box-shadow: 0 0 0 2px var(--card-bg), 0 0 0 4px transparent;
    transition: box-shadow 0.18s, transform 0.18s;
  }
  .dog-tabs button.active .tab-thumb {
    box-shadow: 0 0 0 2px var(--card-bg), 0 0 0 4px var(--primary);
    transform: scale(1.05);
  }
  .tab-thumb img { width: 100%; height: 100%; object-fit: cover; display: block; }
  .tab-name { font-weight: 700; font-size: 0.95rem; line-height: 1.2; }
  .tab-detail { font-size: 0.75rem; line-height: 1.2; }

  /* Showcase */
  .dog-showcase {
    display: grid;
    grid-template-columns: minmax(0, 1fr) minmax(0, 1fr);
    border: 1px solid var(--line-divider, rgba(0,0,0,0.08));
    border-radius: var(--radius-large, 1rem);
    overflow: hidden;
    background: var(--btn-regular-bg);
  }

  .photo-wrap {
    position: relative;
    min-height: 22rem;
    overflow: hidden;
    background: var(--btn-regular-bg);
  }
  .photo-wrap img {
    position: absolute; inset: 0;
    width: 100%; height: 100%; object-fit: cover; display: block;
  }

  .info-wrap {
    display: flex; flex-direction: column;
    gap: 1.25rem;
    padding: 1.4rem 1.4rem 1.3rem;
    min-width: 0;
  }
  .info-head { display: flex; flex-direction: column; gap: 0.55rem; }
  .info-name {
    margin: 0; font-size: 2rem; font-weight: 800; letter-spacing: -0.02em; line-height: 1;
  }
  .info-badges { display: flex; flex-wrap: wrap; gap: 0.4rem; }
  .ibadge {
    font-size: 0.72rem; font-weight: 600; letter-spacing: 0.02em;
    border-radius: 999px; padding: 0.3rem 0.65rem; line-height: 1;
    border: 1px solid var(--line-divider, rgba(0,0,0,0.08));
  }
  .ibadge.age { background: var(--primary); color: white; border-color: var(--primary); }
  .ibadge.born { background: var(--card-bg); color: #374151; }
  :global(html.dark) .ibadge.born { color: rgba(255,255,255,0.85); }

  .dna { display: flex; flex-direction: column; gap: 0.45rem; }
  .dna-label {
    display: flex; justify-content: space-between; align-items: baseline;
    font-size: 0.8rem; font-weight: 600;
  }
  .dna-bar {
    display: flex; gap: 2px;
    height: 0.85rem;
    border-radius: 999px;
    overflow: hidden;
  }
  .dna-segment { flex-basis: 0; min-width: 4px; transition: flex-grow 0.4s ease; }

  @media (max-width: 760px) {
    .dog-showcase { grid-template-columns: 1fr; }
    .photo-wrap { min-height: 0; aspect-ratio: 4 / 3; }
    .info-name { font-size: 1.6rem; }
  }

  @media (prefers-reduced-motion: reduce) {
    .dog-tabs button, .tab-thumb, .dna-segment { transition: none; }
  }
</style>
