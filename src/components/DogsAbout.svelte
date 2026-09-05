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

function getBreedSummary(dog: Dog): string {
	if (dog.breeds.length === 1) return "Purebred";
	return `${dog.breeds.length} breeds`;
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
    <p class="dogs-sub">Meet the pack</p>
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
        <span class="tab-thumb">
          <img src={dog.image} alt="" aria-hidden="true" loading="lazy" style:object-position={dog.heroPos} />
        </span>
        <span class="tab-meta">
          <span class="tab-name">{dog.name}</span>
          <span class="tab-detail">{getBreedSummary(dog)} &middot; {getAge(dog.birthday)}</span>
        </span>
        {#if selected === dog.name}
          <span class="tab-check" aria-hidden="true">&#10003;</span>
        {/if}
      </button>
    {/each}
  </div>

  <div class="dog-showcase" role="tabpanel" aria-label="{active.name} profile">
    <div class="showcase-grid">
      <div class="photo-wrap">
        {#key active.name}
          <img
            src={active.image}
            alt="{active.name} - {getBreedSummary(active)}"
            loading="eager"
            style:object-position={active.heroPos}
          />
        {/key}
        <div class="photo-veil" aria-hidden="true"></div>
        <div class="photo-caption">
          <h3>{active.name}</h3>
          <p>{getAge(active.birthday)} &middot; Born {birthdayFormatter.format(active.birthday)}</p>
        </div>
      </div>

      <div class="info-wrap">
        <div class="info-head">
          <h3 class="info-name">{active.name}</h3>
          <div class="info-badges">
            <span class="ibadge age">{getAge(active.birthday)}</span>
            <span class="ibadge born">Born {birthdayFormatter.format(active.birthday)}</span>
          </div>
          <p class="info-summary">
            {#if active.breeds.length === 1}
              Purebred German Shepherd Embark DNA confirmed.
            {:else}
              {active.breeds.length}-breed mix &middot; Embark DNA as tested.
            {/if}
          </p>
        </div>

        <div class="info-breeds">
          <BreedBars breeds={active.breeds} />
          <p class="embark-footnote">
            {#if active.breeds.length === 1}
              Purebred result Embark DNA.
            {:else}
              {active.name}'s Embark results - {active.breeds.length} breeds as tested.
            {/if}
          </p>
        </div>
      </div>
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
    color: #6b7280;
  }
  :global(html.dark) .dogs-sub { color: rgba(255,255,255,0.58); }

  .dog-tabs {
    display: grid;
    grid-template-columns: repeat(3, minmax(0,1fr));
    gap: 0.6rem;
    margin-bottom: 1rem;
  }
  @media (max-width: 720px) { .dog-tabs { grid-template-columns: 1fr; } }
  .dog-tabs button {
    position: relative;
    display: flex; align-items: center; gap: 0.7rem;
    padding: 0.5rem 0.6rem;
    border-radius: 999px;
    border: 1px solid var(--line-divider, rgba(0,0,0,0.08));
    background: var(--card-bg, #fff);
    cursor: pointer; text-align: left;
    transition: border-color 0.18s, background 0.18s, box-shadow 0.18s, transform 0.15s;
    box-shadow: 0 1px 0 rgba(0,0,0,0.02);
  }
  :global(html.dark) .dog-tabs button {
    background: rgba(255,255,255,0.05);
    border-color: rgba(255,255,255,0.07);
  }
  .dog-tabs button:hover {
    border-color: color-mix(in oklch, var(--primary) 35%, transparent);
    transform: translateY(-1px);
  }
  .dog-tabs button.active {
    background: var(--btn-regular-bg, #f4f4f5);
    border-color: var(--primary);
    box-shadow: 0 4px 16px rgba(0,0,0,0.06);
  }
  :global(html.dark) .dog-tabs button.active {
    background: color-mix(in oklch, var(--primary) 14%, oklch(0.23 0.015 var(--hue)));
  }
  .tab-thumb {
    width: 2.5rem; height: 2.5rem; border-radius: 50%; overflow: hidden;
    flex-shrink: 0; border: 2px solid white;
    box-shadow: 0 1px 6px rgba(0,0,0,0.14);
    background: var(--btn-regular-bg);
  }
  :global(html.dark) .tab-thumb { border-color: rgba(255,255,255,0.9); }
  .tab-thumb img { width: 100%; height: 100%; object-fit: cover; display: block; }
  .tab-meta { display: flex; flex-direction: column; min-width: 0; flex: 1; }
  .tab-name { font-weight: 700; font-size: 0.95rem; line-height: 1.1; color: #111827; }
  :global(html.dark) .tab-name { color: #f3f4f6; }
  .tab-detail { font-size: 0.74rem; line-height: 1.2; color: #6b7280; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; margin-top: 1px; }
  :global(html.dark) .tab-detail { color: rgba(255,255,255,0.60); }
  .tab-check {
    width: 1.35rem; height: 1.35rem; border-radius: 50%;
    display: grid; place-items: center;
    background: var(--primary); color: white;
    font-size: 0.7rem; font-weight: 800; flex-shrink: 0;
  }

  .dog-showcase {
    border: 1px solid var(--line-divider, rgba(0,0,0,0.08));
    border-radius: var(--radius-large, 1rem);
    overflow: hidden;
    background: var(--card-bg, white);
    box-shadow: 0 8px 28px rgba(0,0,0,0.05);
  }
  :global(html.dark) .dog-showcase {
    background: oklch(0.23 0.015 var(--hue));
    border-color: rgba(255,255,255,0.08);
  }
  .showcase-grid {
    display: grid;
    grid-template-columns: 1.05fr 1fr;
  }
  @media (max-width: 760px) { .showcase-grid { grid-template-columns: 1fr; } }

  .photo-wrap {
    position: relative;
    aspect-ratio: 4 / 3;
    overflow: hidden;
    background: var(--btn-regular-bg, #f3f4f6);
  }
  @media (min-width: 761px) { .photo-wrap { aspect-ratio: 4 / 5; } }
  @media (min-width: 1024px) { .photo-wrap { aspect-ratio: 1 / 1; max-height: 30rem; } }
  .photo-wrap img {
    width: 100%; height: 100%; object-fit: cover; display: block;
  }
  .photo-veil {
    position: absolute; inset: 0;
    background: linear-gradient(to top, rgba(0,0,0,0.55) 0%, rgba(0,0,0,0.08) 45%, transparent 70%);
    opacity: 1;
  }
  @media (min-width: 761px) { .photo-veil { opacity: 0; } }
  .photo-caption {
    position: absolute; left: 1rem; right: 1rem; bottom: 0.9rem;
    color: white;
  }
  @media (min-width: 761px) { .photo-caption { display: none; } }
  .photo-caption h3 {
    margin: 0; font-size: 1.6rem; font-weight: 800; letter-spacing: -0.02em;
    text-shadow: 0 1px 12px rgba(0,0,0,0.4);
  }
  .photo-caption p {
    margin: 0.2rem 0 0; font-size: 0.8rem; font-weight: 500;
    opacity: 0.92; text-shadow: 0 1px 8px rgba(0,0,0,0.35);
  }

  .info-wrap {
    display: flex; flex-direction: column;
    padding: 1.25rem 1.3rem 1.2rem;
    gap: 1.15rem;
    min-width: 0;
  }
  @media (max-width: 760px) { .info-wrap { padding-top: 1.1rem; } }
  .info-head { display: flex; flex-direction: column; gap: 0.45rem; }
  @media (max-width: 760px) { .info-head { display: none; } }
  .info-name {
    margin: 0; font-size: 1.75rem; font-weight: 800; letter-spacing: -0.02em;
    color: #111827; line-height: 1;
  }
  :global(html.dark) .info-name { color: #f9fafb; }
  .info-badges { display: flex; flex-wrap: wrap; gap: 0.4rem; margin-top: 0.15rem; }
  .ibadge {
    font-size: 0.72rem; font-weight: 600; letter-spacing: 0.02em;
    border-radius: 999px; padding: 0.28rem 0.6rem; line-height: 1;
    border: 1px solid var(--line-divider, rgba(0,0,0,0.08));
  }
  .ibadge.age {
    background: var(--primary); color: white; border-color: var(--primary);
  }
  .ibadge.born {
    background: var(--btn-regular-bg, #f3f4f6); color: #374151;
  }
  :global(html.dark) .ibadge.born {
    background: rgba(255,255,255,0.08); color: rgba(255,255,255,0.85); border-color: rgba(255,255,255,0.10);
  }
  .info-summary {
    margin: 0.15rem 0 0; font-size: 0.88rem; line-height: 1.45; color: #6b7280;
  }
  :global(html.dark) .info-summary { color: rgba(255,255,255,0.62); }

  .info-breeds { margin-top: auto; }
  .embark-footnote { margin: 0.75rem 0 0; font-size: 0.76rem; color: #6b7280; line-height: 1.4; }
  :global(html.dark) .embark-footnote { color: rgba(255,255,255,0.45); }
</style>

