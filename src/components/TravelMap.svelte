<svelte:head>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin="" />
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
</svelte:head>

<script lang="ts">
  import { onMount } from 'svelte';

  let mapElement: HTMLElement;
  let dogMapElement: HTMLElement;
  let map: any;
  let dogMap: any;
  let dogMarkerLayer: any;
  let selectedDog = "Kyle";

  const tileUrl = "https://tile.openstreetmap.org/{z}/{x}/{y}.png";
  const tileAttribution = "&copy; <a href='https://www.openstreetmap.org/copyright'>OpenStreetMap</a> contributors";

  // Configuration for map categories
  const categoryConfig = {
    home: { color: "#10b981", label: "Lived" }, // Emerald
    work: { color: "#f59e0b", label: "Worked" }, // Amber
    college: { color: "#ef4444", label: "Studied" }, // Red
    visited: { color: "#3b82f6", label: "Visited" } // Blue
  };

  type Category = keyof typeof categoryConfig;
  type Place = {
    name: string;
    place: string;
    state: string;
    coords: [number, number];
    category: Category;
    note: string;
    chapter?: number;
    // Worked remotely, so it doesn't count toward places I've been
    remote?: boolean;
  };

  // `chapter` orders the places that appear in the timeline below the map
  const locations: Place[] = [
    { name: "Peoria", place: "Peoria, IL", state: "IL", coords: [40.6936, -89.5890], category: "home", chapter: 1,
      note: "Where it all started. I grew up here, right on the Illinois River in the heart of central Illinois." },
    { name: "Northern Illinois University", place: "DeKalb, IL", state: "IL", coords: [41.9295, -88.7504], category: "college", chapter: 2,
      note: "I went to college at NIU in DeKalb, about an hour west of Chicago." },
    { name: "Villain Games", place: "Weston, MA (remote)", state: "MA", coords: [42.3668, -71.3031], category: "work", chapter: 3, remote: true,
      note: "Worked part time on Arena of Kings and the unreleased sequel doing QA & product up until I graduated college and got a job at CivicServe." },
    { name: "CivicServe", place: "Bloomington, IL", state: "IL", coords: [40.4788, -88.9927], category: "work", chapter: 4,
      note: "My first full-time QA job, and where I got my start in test automation." },
    { name: "Raleigh", place: "Raleigh, NC", state: "NC", coords: [35.7796, -78.6382], category: "home", chapter: 5,
      note: "Home now. Living in the Research Triangle puts the mountains and the beach both within a few hours' drive." },
    { name: "Aspida", place: "Durham, NC", state: "NC", coords: [35.9189, -78.9669], category: "work", chapter: 6,
      note: "Where I work today as an SDET, building test automation for insurtech." },
    { name: "Chicago", place: "Chicago, IL", state: "IL", coords: [41.8818, -87.6232], category: "visited",
      note: "Saw a Bears game and loved the Shedd Aquarium. I wish I'd visited more often when I lived so close!" },
    { name: "St. Louis", place: "St. Louis, MO", state: "MO", coords: [38.6270, -90.1994], category: "visited",
      note: "We came here even more than Chicago when I was a kid. Just as much fun, and a lot less stressful." },
    { name: "Turkey Run State Park", place: "Marshall, IN", state: "IN", coords: [39.8795, -87.2003], category: "visited",
      note: "Sandstone ravines, ladders, and some of the most fun hiking trails in Indiana." },
    { name: "Wisconsin Dells", place: "Wisconsin Dells, WI", state: "WI", coords: [43.6275, -89.7710], category: "visited",
      note: "A go-to trip from Illinois. We came up here all the time, first on family trips and later with friends." },
    { name: "Natural Bridge", place: "Slade, KY", state: "KY", coords: [37.7768, -83.6833], category: "visited",
      note: "Where I stopped to sleep on the drive down from Peoria when I moved to RTP. A 78-foot sandstone arch right next to the Red River Gorge." },
    { name: "Pigeon Forge", place: "Pigeon Forge, TN", state: "TN", coords: [35.7884, -83.5543], category: "visited",
      note: "My dad's side of the family had a wedding here! I've been back a few times since. I love being surrounded by the Smoky Mountains." },
    { name: "Pisgah National Forest", place: "Western NC", state: "NC", coords: [35.2828, -82.7287], category: "visited",
      note: "Went car camping here with my dog Echo. The highlight was Mount Mitchell and the views of the Blue Ridge Mountains." },
    { name: "Myrtle Beach", place: "Myrtle Beach, SC", state: "SC", coords: [33.6891, -78.8867], category: "visited",
      note: "For some reason, I walked about 8 miles down the beach until 1 AM." },
    { name: "Savannah", place: "Savannah, GA", state: "GA", coords: [32.0811, -81.0911], category: "visited",
      note: "Spanish moss, historic squares, and cobblestone River Street." },
    { name: "Walt Disney World", place: "Orlando, FL", state: "FL", coords: [28.3772, -81.5707], category: "visited",
      note: "Went as a kid. Honestly, I was too young to remember much!" },
    { name: "Universal Orlando", place: "Orlando, FL", state: "FL", coords: [28.4794, -81.4688], category: "visited",
      note: "Went in 2025 to check out the new Epic Universe park. Super Nintendo World was hands-down my favorite." },
    { name: "Washington, D.C.", place: "Washington, D.C.", state: "DC", coords: [38.9072, -77.0369], category: "visited",
      note: "Went here with my family as a kid. I distinctly remember how cool the National Mall was." },
    { name: "New York City", place: "New York, NY", state: "NY", coords: [40.7580, -73.9855], category: "visited",
      note: "Stayed in Flushing, Queens, and headed into Manhattan to watch every screen in Times Square flip at midnight. Kyle has been here too!" },
    { name: "Denver", place: "Denver, CO", state: "CO", coords: [39.7420, -104.9915], category: "visited",
      note: "I visited when I was really young, but it's still the farthest west I've ever been." },
  ];

  const home = locations.find((loc) => loc.name === "Raleigh")!;
  const journey = locations.filter((loc) => loc.chapter).sort((a, b) => a.chapter! - b.chapter!);
  const statesCount = new Set(locations.filter((loc) => !loc.remote).map((loc) => loc.state).filter((s) => s !== "DC")).size;
  const farthestMiles = Math.round(Math.max(...locations.map((loc) => milesBetween(home.coords, loc.coords))));

  let selectedName = "";
  let pinLayer: any;
  const markers = new Map<string, any>();

  function milesBetween([lat1, lon1]: [number, number], [lat2, lon2]: [number, number]) {
    const toRad = (deg: number) => (deg * Math.PI) / 180;
    const a = Math.sin(toRad(lat2 - lat1) / 2) ** 2
      + Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) * Math.sin(toRad(lon2 - lon1) / 2) ** 2;
    return 3959 * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
  }

  function popupHtml(loc: Place) {
    const category = categoryConfig[loc.category];
    return `<b>${loc.name}</b><br><span style="color:${category.color};font-weight:600">${category.label}</span> · ${loc.place}<p style="margin:0.4rem 0 0">${loc.note}</p>`;
  }

  function focusPlace(loc: Place) {
    selectedName = loc.name;
    if (!map) return;
    mapElement.scrollIntoView({ behavior: "smooth", block: "center" });
    map.closePopup();
    map.once("moveend", () => markers.get(loc.name)?.openPopup());
    map.flyTo(loc.coords, 9, { duration: 1.2 });
  }

  const dogLocations = {
    Kyle: [
      { name: "New York City", coords: [40.758, -73.9855], category: "visited" },
      { name: "Raleigh, North Carolina", coords: [35.7796, -78.6382], category: "home" }
    ],
    Echo: [
      { name: "Pisgah National Forest", coords: [35.2828, -82.7287], category: "visited" },
      { name: "Raleigh, North Carolina", coords: [35.7796, -78.6382], category: "home" },
      { name: "Middlesex, North Carolina", coords: [35.7902, -78.2042], category: "home" }
    ],
    River: [
      { name: "Raleigh, North Carolina", coords: [35.7796, -78.6382], category: "home" },
      { name: "Middlesex, North Carolina", coords: [35.7902, -78.2042], category: "home" }
    ]
  };

  function renderDogLocations(Leaflet: any) {
    if (!dogMap) return;

    if (dogMarkerLayer) dogMarkerLayer.clearLayers();
    dogMarkerLayer = Leaflet.layerGroup().addTo(dogMap);

    const dogPoints = dogLocations[selectedDog as keyof typeof dogLocations];
    dogPoints.forEach((loc) => {
      const category = categoryConfig[loc.category as keyof typeof categoryConfig];
      Leaflet.circleMarker(loc.coords as [number, number], {
        radius: 8,
        fillColor: category.color,
        color: "#000",
        weight: 1,
        opacity: 1,
        fillOpacity: 0.8
      })
        .bindPopup(`<b>${loc.name}</b><br>${category.label}`)
        .addTo(dogMarkerLayer);
    });

    dogMap.fitBounds(dogMarkerLayer.getBounds(), { padding: [40, 40], maxZoom: 6 });
  }

  function selectDog(dog: keyof typeof dogLocations) {
    selectedDog = dog;
    // @ts-ignore
    if (typeof L !== "undefined") renderDogLocations(L);
  }

  onMount(() => {
    let mapInstance: any;

    const initMap = () => {
      // @ts-ignore
      if (typeof L === 'undefined') {
        setTimeout(initMap, 100);
        return;
      }

      try {
        // @ts-ignore
        const Leaflet = L;

        if (mapElement) {
          mapInstance = Leaflet.map(mapElement).setView([39.8283, -98.5795], 4); // Center of US

          Leaflet.tileLayer(tileUrl, {
            attribution: tileAttribution,
            maxZoom: 19
          }).addTo(mapInstance);

          // Add markers
          pinLayer = Leaflet.featureGroup().addTo(mapInstance);
          locations.forEach(loc => {
            const category = categoryConfig[loc.category];
            const marker = Leaflet.circleMarker(loc.coords, {
              radius: 8,
              fillColor: category.color,
              color: "#000",
              weight: 1,
              opacity: 1,
              fillOpacity: 0.8
            })
            .bindPopup(popupHtml(loc), { maxWidth: 260 })
            .on("click", () => { selectedName = loc.name; })
            .addTo(pinLayer);
            markers.set(loc.name, marker);
          });
          mapInstance.fitBounds(pinLayer.getBounds(), { padding: [30, 30] });

          // Add Legend
          const legend = Leaflet.control({ position: "bottomright" });

          legend.onAdd = function () {
            const div = Leaflet.DomUtil.create("div", "info legend");
            
            div.style.backgroundColor = "white";
            div.style.padding = "10px";
            div.style.borderRadius = "5px";
            div.style.boxShadow = "0 0 15px rgba(0,0,0,0.2)";

            let labels = ["<strong>Key</strong>"];
            
            Object.values(categoryConfig).forEach((category) => {
              labels.push(
                `<div style="display: flex; align-items: center; margin-top: 5px;">
                  <i style="background:${category.color}; width: 18px; height: 18px; border-radius: 50%; display: inline-block; margin-right: 8px; border: 1px solid #000;"></i> 
                  <span>${category.label}</span>
                </div>`
              );
            });

            div.innerHTML = labels.join("");
            return div;
          };

          legend.addTo(mapInstance);
          
          map = mapInstance;

          if (dogMapElement) {
            dogMap = Leaflet.map(dogMapElement).setView([35.7796, -78.6382], 6);
            Leaflet.tileLayer(tileUrl, {
              attribution: tileAttribution,
              maxZoom: 19
            }).addTo(dogMap);
            renderDogLocations(Leaflet);

          }
        }
      } catch (e) {
        console.error("Failed to load Leaflet map", e);
      }
    };

    initMap();

    return () => {
      if (mapInstance) {
        mapInstance.remove();
      }
      if (dogMap) dogMap.remove();
    };
  });
</script>

<div class="stats">
  <div class="stat"><span class="stat-value">{locations.length}</span><span class="stat-label text-50">pins on the map</span></div>
  <div class="stat"><span class="stat-value">{statesCount}</span><span class="stat-label text-50">states + D.C.</span></div>
  <div class="stat"><span class="stat-value">{farthestMiles.toLocaleString()}</span><span class="stat-label text-50">miles from home to my farthest pin</span></div>
</div>

<div class="map-container">
  <div bind:this={mapElement} class="map"></div>
</div>

<section class="section">
  <ol class="journey">
    {#each journey as loc, i}
      <li>
        <button class="journey-step" class:selected={selectedName === loc.name} on:click={() => focusPlace(loc)}>
          <span class="dot" style="background:{categoryConfig[loc.category].color}"></span>
          <span class="journey-chapter text-50">Chapter {i + 1} · {categoryConfig[loc.category].label}</span>
          <span class="journey-name text-90">{loc.name}</span>
          <span class="journey-place text-50">{loc.place}</span>
          <span class="journey-note text-75">{loc.note}</span>
        </button>
      </li>
    {/each}
  </ol>
</section>

<style>
  .stats {
    display: grid;
    gap: 0.75rem;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    margin-bottom: 1rem;
  }

  .stat {
    background: var(--btn-regular-bg);
    border-radius: 0.75rem;
    display: flex;
    flex-direction: column;
    padding: 0.75rem 1rem;
  }

  .stat-value {
    color: var(--primary);
    font-size: 1.75rem;
    font-weight: 700;
    line-height: 1.2;
  }

  .stat-label {
    font-size: 0.8rem;
  }

  .section {
    margin-top: 1.75rem;
  }

  .journey {
    border-left: 2px dashed var(--line-divider);
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
    list-style: none;
    margin: 0 0 0 0.5rem;
    padding: 0;
  }

  .journey-step {
    background: none;
    border: 0;
    border-radius: 0.75rem;
    cursor: pointer;
    display: grid;
    font: inherit;
    padding: 0.6rem 0.9rem 0.6rem 1.5rem;
    position: relative;
    text-align: left;
    transition: background 0.15s;
    width: 100%;
  }

  .journey-step:hover,
  .journey-step.selected {
    background: var(--btn-regular-bg);
  }

  .journey-step .dot {
    border: 2px solid var(--card-bg);
    border-radius: 50%;
    height: 0.9rem;
    left: -0.5rem;
    position: absolute;
    top: 1rem;
    width: 0.9rem;
  }

  .journey-chapter {
    font-size: 0.75rem;
    letter-spacing: 0.04em;
    text-transform: uppercase;
  }

  .journey-name {
    font-size: 1.1rem;
    font-weight: 700;
  }

  .journey-place {
    font-size: 0.85rem;
  }

  .journey-note {
    margin-top: 0.25rem;
  }

  .map-container {
    width: 100%;
    height: 600px;
    border-radius: 1rem;
    overflow: hidden;
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
  }

  .map {
    width: 100%;
    height: 100%;
    z-index: 1;
  }

  .dogs-section {
    margin-top: 2.5rem;
  }

  h2 {
    color: var(--primary);
    font-size: 1.5rem;
    font-weight: 700;
    margin: 0 0 1rem;
  }

  .dog-tabs {
    display: flex;
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

  .dog-map-container {
    height: 450px;
    position: relative;
  }

  .dog-map-key {
    align-items: flex-start;
    background: white;
    border-radius: 5px;
    bottom: 1rem;
    box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
    color: #222;
    display: flex;
    flex-direction: column;
    gap: 0.3rem;
    padding: 0.65rem;
    position: absolute;
    right: 1rem;
    z-index: 2;
  }

  .dog-map-key span {
    align-items: center;
    display: flex;
    gap: 0.45rem;
  }

  .dog-map-key i {
    border: 1px solid #000;
    border-radius: 50%;
    display: inline-block;
    height: 1rem;
    width: 1rem;
  }

  .dog-map-key .lived { background: #10b981; }
  .dog-map-key .visited { background: #3b82f6; }

  @media (max-width: 640px) {
    .stat-value {
      font-size: 1.35rem;
    }

    .map-container {
      height: 420px;
    }
  }
</style>
