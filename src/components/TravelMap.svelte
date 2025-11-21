<svelte:head>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin="" />
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
</svelte:head>

<script lang="ts">
  import { onMount } from 'svelte';

  let mapElement: HTMLElement;
  let map: any;

  // Configuration for map categories
  const categoryConfig = {
    home: { color: "#10b981", label: "Home / Grew Up" }, // Emerald
    work: { color: "#f59e0b", label: "Work" }, // Amber
    college: { color: "#ef4444", label: "College" }, // Red
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
  ];

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
    };
  });
</script>

<div class="map-container">
  <div bind:this={mapElement} class="map"></div>
</div>

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
</style>
