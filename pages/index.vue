<template>
  <div id="root">
    <div class="container">
      <ais-instant-search :search-client="searchClient" index-name="Maps_Magik">
        <ais-search-box placeholder="Search Name/Company" class="searchbox" />
        <ais-hits :transform-items="transformItems" class="search-result" />
        <div class="search-result-list">
          <div v-if="input">
            <div v-if="markers.length == 0" class="search-result-card">
              No Such User/Company
            </div>
            <div v-for="marker in markers" :key="marker.objectId">
              <div class="search-result-card" @click="centerMap(marker)">
                <div class="search-result-card-name">
                  <p>{{ marker.fullName }}</p>
                </div>
                <div class="search-result-card-company">
                  <p>{{ marker.companyName }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
        <Map :markers="markers" :focused="focused" />
      </ais-instant-search>
    </div>
  </div>
</template>

<script>
import { AisInstantSearch, AisSearchBox, AisHits } from "vue-instantsearch";
import algoliasearch from "algoliasearch/lite";
import "instantsearch.css/themes/algolia-min.css";
import Map from "~/components/Map.vue";

export default {
  name: "IndexPage",
  components: {
    AisInstantSearch,
    AisSearchBox,
    AisHits,
    Map,
  },
  data() {
    return {
      searchClient: algoliasearch(
        "ZFBW2IRNZ0",
        "ada9174d49c8779e6826e896c2e1418d"
      ),
      markers: [],
      input: "",
      focused: [],
    };
  },
  methods: {
    transformItems(items) {
      // Getting input value so show or hide results
      let input = Array.from(
        document.getElementsByClassName("ais-SearchBox-input")
      )[0]?.value;
      this.input = input;

      // Getting all markers that are results
      this.markers = [];
      if (this.input) {
        items.map((item) => {
          if (item["location.lat"] && item["location.lng"])
            this.markers.push(item);
        });
        return items.map((item) => ({
          ...item,
        }));
      }
    },
    // Getting cooridates of marker choosen from results
    centerMap(marker) {
      let LngLat = [marker["location.lng"], marker["location.lat"]];
      this.focused = LngLat;
    },
  },
};
</script>

<style >
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {
  margin: 0 auto;
  position: absolute;
  top: 0;
}
.searchbox {
  z-index: 1;
  margin: 20px 0px 0px 20px;
}
input {
  outline: none;
}
.search-result {
  display: none;
}
.search-result-list {
  background: white;
  height: auto;
  max-height: 50vh;
  overflow-y: auto;
  margin: 0px 0px 0px 20px;
}

.search-result-list::-webkit-scrollbar {
  display: none;
}
.search-result-list {
  -ms-overflow-style: none;
  scrollbar-width: none;
}

.search-result-card {
  cursor: pointer;
  padding: 5px 20px;
  border-bottom: 1px solid gray;
}
.search-result-card-company {
  color: gray;
  font-size: 0.75rem;
}
</style>