<template>
  <div id="root">
    <div class="search-container">
      <ais-instant-search :search-client="searchClient" index-name="Maps_Magik">
        <ais-search-box placeholder="Search here…" class="searchbox" />
        <ais-hits :transform-items="transformItems" class="search-result" />
        <div class="search-result-list">
          <div v-if="markers.length == 0" class="search-result-card">No Such User/Company</div>
          <div v-for="marker in markers" :key="marker.objectId">
            <div class="search-result-card">
              <div class="search-result-card-name">
                <p>{{ marker.fullName }}</p>
              </div>
              <div class="search-result-card-company">
                <p>{{ marker.companyName }}</p>
              </div>
            </div>
          </div>
        </div>
        <Map :markers="markers" />
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
    };
  },
  methods: {
    transformItems(items) {
      this.markers = [];
      items.map((item) => {
        if (item["location.lat"] && item["location.lng"])
          this.markers.push(item);
      });
      return items.map((item) => ({
        ...item,
      }));
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
.ais-Highlight-highlighted {
  background: rgb(134, 27, 102);
  color: white;
  font-style: normal;
}

.search-container {
  margin: 0 auto;
  position: absolute;
  top: 0;
}
.searchbox {
  z-index: 1;
}
.search-result {
  display: none;
}
.search-result-list {
  background: white;
  height: 50vh;
  overflow-y: auto;
}
.search-result-card {
  padding: 5px 20px;
}
.search-result-card-company {
  color: gray;
  font-size: 0.75rem;
}
</style>