<template>
  <div>
    <div id="map"></div>
    <div v-if="markers.length > 0">
      <div v-for="marker in markers" :key="marker.id"></div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["markers"],
  mounted() {
    // Planting Initial Map
    const mapboxgl = require("mapbox-gl");
    this.map = new mapboxgl.Map({
      accessToken:
        "pk.eyJ1IjoiZGl2eWFuNWgiLCJhIjoiY2wyc3EzeWhvMDEzeDNrbjJndHNxNG9kMiJ9.xz5kTfAAHPpP0kG3P_qzHg",
      container: "map",
      attributionControl: false,
      style: "mapbox://styles/mapbox/light-v9",
      center: [77.2, 28.6],
      zoom: 3,
      pitch: 0,
    });
  },
  updated() {
    //removing any existing markers
    let exMarkers = Array.from(document.getElementsByClassName("marker"));
    exMarkers.map((marker) => {
      marker.parentNode.removeChild(marker);
    });

    const mapboxgl = require("mapbox-gl");

    this.markers.map((marker) => {
      const LngLat = [marker["location.lng"], marker["location.lat"]];
      // adding markers to map
      const el = document.createElement("div");
      el.className = "marker";
      el.innerHTML = `
      <img class="marker-photo" src="${marker.photo}"/>
      <p>${marker.fullName}</p>
      `;

      // make a marker for each feature and add to the map
      new mapboxgl.Marker(el).setLngLat(LngLat).addTo(this.map);
    });
  },
  head: {
    link: [
      {
        rel: "stylesheet",
        href: "https://api.mapbox.com/mapbox-gl-js/v1.10.0/mapbox-gl.css",
      },
    ],
  },
};
</script>

<style>
@import url("https://api.tiles.mapbox.com/mapbox-gl-js/v0.45.0/mapbox-gl.css");

#map {
  position: absolute;
  top: 0;
  z-index: -1;
  height: 100vh;
  width: 100vw;
}
</style>