<template>
  <div id="root">
    <div class="container">
      <ais-instant-search :search-client="searchClient" index-name="Maps_Magik">
        <ais-configure>
          <ais-search-box placeholder="Search Name/Company" class="searchbox" />
          <ais-hits :transform-items="transformItems" class="search-result" />
          <div v-if="showResult" class="search-result-list">
            <!-- <div v-if="input"> -->
            <div v-if="markers.length == 0" class="search-result-card">
              No Such User/Company
            </div>
            <div v-else>
              <div v-for="marker in markers" :key="marker.id">
                <div class="search-result-card" @click="centerMap(marker)">
                  <div>
                    <span class="search-result-card-name"
                      >{{ marker.fullName }} -
                    </span>
                    <span class="search-result-card-location">{{
                      marker.companyName
                    }}</span>
                  </div>
                  <div class="search-result-card-location">
                    <p>
                      {{ marker["location.city"] }},
                      {{ marker["location.country"] }}
                    </p>
                  </div>
                </div>
              </div>
              <ais-pagination />
            </div>
            <!-- </div> -->
          </div>
          <Map :markers="markers" :focused="focused" />
        </ais-configure>
      </ais-instant-search>
    </div>
  </div>
</template>

<script>
import {
  AisInstantSearch,
  AisSearchBox,
  AisHits,
  AisConfigure,
  AisPagination,
} from "vue-instantsearch";
import algoliasearch from "algoliasearch/lite";
import "instantsearch.css/themes/algolia-min.css";
import Map from "~/components/Map.vue";

export default {
  name: "IndexPage",
  components: {
    AisInstantSearch,
    AisSearchBox,
    AisHits,
    AisConfigure,
    AisPagination,
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
      showResult: true,
    };
  },
  methods: {
    transformItems(items) {
      this.showResult = true;
      // Getting input value so show or hide results
      let input = Array.from(
        document.getElementsByClassName("ais-SearchBox-input")
      )[0]?.value;
      this.input = input;

      // Getting all markers that are results
      this.markers = [];
      items.map((item) => {
        if (item["location.lat"] && item["location.lng"])
          this.markers.push(item);
      });

      // Re-odering markers array if input
      if (this.input) {
        let matchMarkers = [];
        let noMatchMarkers = [];
        this.markers.map((el) => {
          if (el.fullName.toUpperCase().startsWith(input.toUpperCase())) {
            matchMarkers.push(el);
          } else {
            noMatchMarkers.push(el);
          }
        });
        this.markers = [...matchMarkers, ...noMatchMarkers];
      }

      return this.markers.map((item) => ({
        ...item,
      }));
    },
    // Getting cooridates of marker choosen from results
    centerMap(marker) {
      let inputField = Array.from(
        document.getElementsByClassName("ais-SearchBox-input")
      )[0];
      inputField.value = marker.fullName;
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
  width: 40vw;
  max-width: 400px;
  margin: 35px 0px 0px 45px;
  box-shadow: 0 0px 10px 0 rgba(0, 0, 0, 0.15), 0 6px 20px 0 rgba(0, 0, 0, 0.1);
  border-radius: 5px;
}
input {
  outline: none;
}
.search-result,
.ais-SearchBox-reset {
  display: none;
}
.search-result-list {
  background: white;
  height: auto;
  max-height: 50vh;
  overflow-y: auto;
  margin: 10px 0px 0px 45px;
  border-radius: 5px;
  box-shadow: 0 0px 10px 0 rgba(0, 0, 0, 0.1), 0 6px 20px 0 rgba(0, 0, 0, 0.1);
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
  border-top: 1px solid rgba(128, 128, 128, 0.45);
}
.search-result-card-location {
  color: gray;
  font-size: 0.75rem;
}
</style>