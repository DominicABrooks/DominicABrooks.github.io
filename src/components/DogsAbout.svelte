<script lang="ts">
const dogs = {
	Echo: {
		birthday: new Date(2022, 10, 6),
		image: "/images/dogs/Echo.png",
		details: "100% German Shepherd",
	},
	River: {
		birthday: new Date(2021, 4, 9),
		image: "/images/dogs/River.png",
		details:
			"33.37% Golden Retriever, 18.1% Doberman Pinscher, 16.2% Rottweiler, 16% Chow Chow, 10.4% Supermutt, and 5.6% Cocker Spaniel",
	},
	Kyle: {
		birthday: new Date(2025, 10, 15),
		image: "/images/dogs/Kyle.png",
		details: "Embark results are still pending.",
	},
};

let selectedDog: keyof typeof dogs = "Kyle";

function getAge(birthday: Date) {
	const today = new Date();
	let years = today.getFullYear() - birthday.getFullYear();
	let months = today.getMonth() - birthday.getMonth();

	if (today.getDate() < birthday.getDate()) months -= 1;
	if (months < 0) {
		years -= 1;
		months += 12;
	}

	return `${years} year${years === 1 ? "" : "s"}, ${months} month${months === 1 ? "" : "s"}`;
}

const birthdayFormatter = new Intl.DateTimeFormat("en-US", {
	month: "long",
	day: "numeric",
	year: "numeric",
});
</script>

<section class="dogs-section" aria-labelledby="dogs-heading">
  <h2 id="dogs-heading">Dogs</h2>
  <div class="dog-tabs" role="tablist" aria-label="Dog profiles">
    {#each Object.keys(dogs) as dog}
      <button
        type="button"
        role="tab"
        aria-selected={selectedDog === dog}
        class:active={selectedDog === dog}
        on:click={() => (selectedDog = dog as keyof typeof dogs)}
      >{dog}</button>
    {/each}
  </div>

  <div class="dog-profile" role="tabpanel">
    <h3>{selectedDog}</h3>
    <img class="dog-photo" class:echo-photo={selectedDog === "Echo"} src={dogs[selectedDog].image} alt={selectedDog} />
    <p><strong>Birthday:</strong> {birthdayFormatter.format(dogs[selectedDog].birthday)}</p>
    <p><strong>Age:</strong> {getAge(dogs[selectedDog].birthday)}</p>
    {#if selectedDog === "River"}
      <p><strong>Embark results:</strong></p>
      <div class="embark-chart" aria-label="River's Embark breed results">
        <div
          class="pie-chart"
          role="img"
          aria-label="33.37% Golden Retriever, 18.1% Doberman Pinscher, 16.2% Rottweiler, 16% Chow Chow, 10.4% Supermutt, and 5.6% Cocker Spaniel"
        ></div>
        <ul class="pie-legend">
          <li><i class="golden"></i>33.37% Golden Retriever</li>
          <li><i class="doberman"></i>18.1% Doberman Pinscher</li>
          <li><i class="rottweiler"></i>16.2% Rottweiler</li>
          <li><i class="chow"></i>16% Chow Chow</li>
          <li><i class="supermutt"></i>10.4% Supermutt</li>
          <li><i class="cocker"></i>5.6% Cocker Spaniel</li>
        </ul>
      </div>
    {:else}
      <p><strong>Embark:</strong> {dogs[selectedDog].details}</p>
    {/if}
  </div>
</section>

<style>
  .dogs-section {
    margin-top: 2rem;
  }

  h2 {
    color: var(--tw-prose-headings, var(--tw-prose-body, white));
    font-size: 1.5rem;
    font-weight: 700;
    margin: 0 0 1rem;
  }

  .dog-tabs {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    margin-bottom: 1rem;
  }

  .dog-tabs button {
    border: 1px solid var(--primary);
    border-radius: 999px;
    background: transparent;
    color: var(--primary);
    cursor: pointer;
    font: inherit;
    padding: 0.45rem 1rem;
  }

  .dog-tabs button.active,
  .dog-tabs button:hover {
    background: var(--primary);
    color: var(--card-bg, white);
  }

  .dog-profile {
    border: 1px solid var(--line-divider, var(--primary));
    border-radius: var(--radius-large);
    color: var(--tw-prose-body, white);
    padding: 1.25rem;
  }

  h3 {
    font-size: 1.25rem;
    font-weight: 700;
    margin: 0 0 0.5rem;
  }

  .dog-photo {
    border-radius: 0.75rem;
    display: block;
    height: 15rem;
    margin: 0.45rem 0 1rem;
    object-fit: cover;
    width: min(100%, 20rem);
  }

  .dog-photo.echo-photo {
    object-position: center 65%;
  }

  p {
    margin: 0.35rem 0;
  }

  .embark-chart {
    align-items: center;
    display: flex;
    flex-wrap: wrap;
    gap: 1.25rem;
    margin-top: 0.5rem;
  }

  .pie-chart {
    background: conic-gradient(
      #e9b949 0% 33.37%,
      #5b89d6 33.37% 51.47%,
      #d85b63 51.47% 67.67%,
      #9d79c7 67.67% 83.67%,
      #55ad8b 83.67% 94.07%,
      #ec9a65 94.07% 100%
    );
    border-radius: 50%;
    height: 10rem;
    width: 10rem;
  }

  .pie-legend {
    list-style: none;
    margin: 0;
    padding: 0;
  }

  .pie-legend li {
    align-items: center;
    display: flex;
    gap: 0.45rem;
    margin: 0.25rem 0;
  }

  .pie-legend i,
  .dog-map-key i {
    border-radius: 50%;
    display: inline-block;
    height: 0.8rem;
    width: 0.8rem;
  }

  .golden { background: #e9b949; }
  .doberman { background: #5b89d6; }
  .rottweiler { background: #d85b63; }
  .chow { background: #9d79c7; }
  .supermutt { background: #55ad8b; }
  .cocker { background: #ec9a65; }
</style>
