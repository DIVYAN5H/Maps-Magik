<template>
  <div>
    <div id="map"></div>
    <div class="search">
      <input v-model="input" @input="searchUsers()" class="search-box" type="text" />
      <div id="search-results">
        <div
          v-if="input.length > 0"
          v-for="user in showMarkers"
          class="search-result"
        >
          {{ user.name }}
        </div>
        <div v-if="showMarkers.length == 0">no such user</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DepotsMap",
  data() {
    return {
      allMarkers: [],
      showMarkers: [],
      lastSearch: "",
      input: "",
    };
  },
  mounted() {
    // Planting Initial Map
    const mapboxgl = require("mapbox-gl");
    this.map = new mapboxgl.Map({
      accessToken:
        "pk.eyJ1IjoiZGl2eWFuNWgiLCJhIjoiY2wyc3EzeWhvMDEzeDNrbjJndHNxNG9kMiJ9.xz5kTfAAHPpP0kG3P_qzHg",
      container: "map",
      style: "mapbox://styles/mapbox/streets-v11",
      center: [77.2, 28.6],
      zoom: 3,
      pitch: 0,
    });

    // Temp Fetching all Users Data
    fetch("/allUsers.json")
      .then((res) => res.json())
      .then((data) => {
        this.allMarkers = data;
        this.showMarkers = data;
        // adding all markers
        this.addMarkers();
      });
  },
  methods: {
    addMarkers() {
      //removing any existing markers
      let exMarkers = Array.from(document.getElementsByClassName("marker"));
      exMarkers.map((marker) => {
        marker.parentNode.removeChild(marker);
      });

      const mapboxgl = require("mapbox-gl");

      this.showMarkers.map((marker) => {
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
    searchUsers() {
      if (this.lastSearch.length > this.input.length) {
        //--- Run for each user to check even eleminated earlier
        this.showMarkers = [];
        this.allMarkers.map((marker) => {
          // console.log("Checked every marker");
          if (marker.name.toLowerCase().startsWith(this.input.toLowerCase()))
            this.showMarkers.push(marker);
        });
        this.addMarkers();
      } else {
        //--- Only run on already skimmed array but doesn't work if backspaced
        this.showMarkers = this.showMarkers.filter((marker) => {
          // console.log("Skimmed exisiting marker");
          return marker.name.toLowerCase().startsWith(this.input.toLowerCase());
        });
        this.addMarkers();
      }
      this.lastSearch = this.input;
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
  height: 75vh;
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