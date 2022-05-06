<template>
  <div>
    <div id="map"></div>
    <SearchBar
      :allMarkers="allMarkers"
      :addMarkers="addMarkers"
      @updateMarkers="addMarkers($event)"
    />
    <SideBar :allMarkers="allMarkers" />
  </div>
</template>

<script>
import SearchBar from "~/components/TempA.vue";
import SideBar from "~/components/TempB.vue";
export default {
  components: { SearchBar, SideBar },
  name: "DepotsMap",
  data() {
    return {
      allMarkers: [],
    };
  },
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

    // Temp Fetching all Users Data
    fetch("/allUsers.json")
      .then((res) => res.json())
      .then((data) => {
        this.allMarkers = data;
        // adding all markers
        this.addMarkers(data);
      });
  },
  methods: {
    addMarkers(arr) {
      //removing any existing markers
      let exMarkers = Array.from(document.getElementsByClassName("marker"));
      exMarkers.map((marker) => {
        marker.parentNode.removeChild(marker);
      });

      const mapboxgl = require("mapbox-gl");

      arr.map((marker) => {
        const LngLat = [marker.location.lng, marker.location.lat];
        // adding markers to map
        const el = document.createElement("div");
        el.className = "marker";
        el.innerHTML = `<img class="marker-photo" src="${marker.photoUrl}"/>`;

        // make a marker for each feature and add to the map
        new mapboxgl.Marker(el).setLngLat(LngLat).addTo(this.map);

        // adding pop for marker
        new mapboxgl.Marker(el)
          .setLngLat(LngLat)
          .setPopup(
            new mapboxgl.Popup({ offset: 25 }).setHTML(
              `<img class="marker-photo" src="${marker.photoUrl}"/><h3>${marker.name}</h3>`
            )
          )
          .addTo(this.map);
      });
    },
  },
};
</script>

<style lang="postcss">
@import url("https://api.mapbox.com/mapbox-gl-js/v1.3.0/mapbox-gl.css");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#map {
  height: 100vh;
}
.marker {
  background: black;
  background-size: cover;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
}
.marker-photo {
  height: 45px;
  width: 45px;
  border-radius: 50%;
}
.mapboxgl-popup {
  max-width: 200px;
}

.mapboxgl-popup-content {
  text-align: center;
  font-family: "Open Sans", sans-serif;
}
</style>