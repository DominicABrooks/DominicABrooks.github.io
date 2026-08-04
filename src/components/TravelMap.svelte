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

  // Configuration for map categories
  const categoryConfig = {
    home: { color: "#10b981", label: "Lived" }, // Emerald
    work: { color: "#f59e0b", label: "Worked" }, // Amber
    college: { color: "#ef4444", label: "Studied" }, // Red
    visited: { color: "#3b82f6", label: "Visited" } // Blue
  };

  // Actual data points from the provided Google Maps link
  const locations = [
    { name: "Walt Disney World", coords: [28.3772, -81.5707], category: "visited" },
    { name: "Turkey Run State Park", coords: [39.8795, -87.2003], category: "visited" },
    { name: "Northern Illinois! I went to college here", coords: [41.9295, -88.7504], category: "college" },
    { name: "Raleigh! I live here", coords: [35.7796, -78.6382], category: "home" },
    { name: "Myrtle Beach", coords: [33.6891, -78.8867], category: "visited" },
    { name: "Pisgah National Forest", coords: [35.2828, -82.7287], category: "visited" },
    { name: "Chicago", coords: [41.8818, -87.6232], category: "visited" },
    { name: "Wisconsin Dells", coords: [43.6275, -89.7710], category: "visited" },
    { name: "Pigeon Forge", coords: [35.7884, -83.5543], category: "visited" },
    { name: "Universal Studios", coords: [28.4794, -81.4688], category: "visited" },
    { name: "Washington DC", coords: [38.9072, -77.0369], category: "visited" },
    { name: "Denver", coords: [39.7420, -104.9915], category: "visited" },
    { name: "Aspida! I work here", coords: [35.9189, -78.9669], category: "work" },
	{ name: "CivicServe! My first full-time QA job", coords: [40.4788, -88.9927], category: "work" },
	// Peoria
	{ name: "Peoria Illinois! I grew up here!", coords: [40.6936, -89.5890], category: "home" },
	// St louis
	{ name: "St Louis", coords: [38.6270, -90.1994], category: "visited" },
	// Natural bridge Kentucky
	{ name: "Natural Bridge", coords: [37.7768, -83.6833], category: "visited" },
	// Savana Georgia 
	{ name: "Savannah", coords: [32.0811, -81.0911], category: "visited" },
    // Manhattan / New York City
  { name: "New York City", coords: [40.7580, -73.9855], category: "visited" },
  ];

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

          Leaflet.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>',
            subdomains: 'abcd',
            maxZoom: 20
          }).addTo(mapInstance);

          // Add markers
          locations.forEach(loc => {
            const category = categoryConfig[loc.category as keyof typeof categoryConfig] || categoryConfig.visited;
            Leaflet.circleMarker(loc.coords as [number, number], {
              radius: 8,
              fillColor: category.color,
              color: "#000",
              weight: 1,
              opacity: 1,
              fillOpacity: 0.8
            })
            .bindPopup(`<b>${loc.name}</b>`)
            .addTo(mapInstance);
          });

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
            Leaflet.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
              attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>',
              subdomains: 'abcd',
              maxZoom: 20
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

<div class="map-container">
  <div bind:this={mapElement} class="map"></div>
</div>

<section class="dogs-section" aria-labelledby="dogs-heading">
  <h2 id="dogs-heading">Dogs</h2>
  <div class="dog-tabs" role="tablist" aria-label="Dog maps">
    {#each Object.keys(dogLocations) as dog}
      <button
        type="button"
        role="tab"
        aria-selected={selectedDog === dog}
        class:active={selectedDog === dog}
        on:click={() => selectDog(dog as keyof typeof dogLocations)}
      >{dog}</button>
    {/each}
  </div>
  <div class="map-container dog-map-container">
    <div bind:this={dogMapElement} class="map"></div>
    <div class="dog-map-key" aria-label="Map key">
      <strong>Key</strong>
      <span><i class="lived"></i>Lived</span>
      <span><i class="visited"></i>Visited</span>
    </div>
  </div>
</section>

<style>
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
</style>
