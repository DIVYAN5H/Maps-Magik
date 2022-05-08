<template>
  <div>
    <div id="map"></div>
  </div>
</template>

<script>
export default {
  props: ["markers", "focused"],
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
      zoom: 7,
      pitch: 0,
    });
  },
  // Moving map if clicked on a result
  watch: {
    focused: function () {
      this.map.flyTo({
        center: this.focused,
        zoom: 11,
      });
    },
    markers: function () {
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

        if (!marker.photo) {
          marker.photo = "https://robohash.org/EUX.png?set=set1&size=64x64";
        }

        el.innerHTML = `
      <img class="marker-photo" src="${marker.photo}"/>
      <div class="marker-content">
      <p class="marker-name">${marker.fullName}</p>
      <p class="marker-company">${marker.companyName}</p>
      </div>
      `;

        // make a marker for each feature and add to the map
        new mapboxgl.Marker(el).setLngLat(LngLat).addTo(this.map);
      });
    },
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
.mapboxgl-ctrl {
  display: none !important;
}
.marker:hover {
  box-shadow: 0 0px 20px 0 rgba(0, 0, 0, 0.4), 0 0px 10px 0 rgba(0, 0, 0, 0.36);
}
.marker{
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  background: white;
  padding: 8px;
  border-radius: 5px;
  box-shadow: 0 0px 20px 0 rgba(0, 0, 0, 0.2), 0 0px 10px 0 rgba(0, 0, 0, 0.19);
}
.marker-photo {
  background: rgba(48, 44, 44, 0.411);
  border-radius: 50%;
}
.marker-content {
  margin-left: 10px;
}
.marker-company {
  color: gray;
}
</style>