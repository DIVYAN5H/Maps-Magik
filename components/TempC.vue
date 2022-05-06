<template>
  <div class="search-box">
    <ais-instant-search-ssr>
      <ais-search-box />
      <ais-configure :attributesToSnippet="['bodyPlainText']" :hits-per-page.camel='5'>
        <ais-autocomplete>
          <!-- <div slot-scope="{ curentRefinement, indices, refine }"> -->
          <div slot-scope="{indices}">
            <!-- <input
              type="search"
              ref="searchInput"
              :value="curentRefinement"
              @input="refine($event.currentTarget.value)"
              autocomplete="off"
            /> -->
            <div v-for="section in indices" :key="section.indexName">
              <div
                v-for="hit in section.hits"
                :key="hit.objectID"
                class="search-result-list"
              >
                <div class="search-result-card">
                  <div class="search-result-card-name">
                    {{ hit.fullName }}
                  </div>
                  <div class="search-result-card-company">
                    {{ hit.companyName }}
                  </div>
                </div>
              </div>
            </div>
          </div>
        </ais-autocomplete>
      </ais-configure>
    </ais-instant-search-ssr>
  </div>
</template>

<script>
import {
  AisInstantSearchSsr,
  AisSearchBox,
  createServerRootMixin,
  AisConfigure,
  AisAutocomplete,
  AisHighlight,
} from "vue-instantsearch";
import algoliasearch from "algoliasearch/lite";
import _renderToString from "vue-server-renderer/basic";

const searchClient = algoliasearch(
  "ZFBW2IRNZ0",
  "ada9174d49c8779e6826e896c2e1418d"
);

export default {
  mixins: [
    createServerRootMixin({
      searchClient,
      indexName: "Maps_Magik",
    }),
  ],
  components: {
    AisInstantSearchSsr,
    AisSearchBox,
    AisConfigure,
    AisAutocomplete,
    AisHighlight,
  },
  head() {
    return {
      link: [
        {
          rel: "stylesheet",
          href: "https://unpkg.com/instantsearch.css@7.1.0/themes/algolia-min.css",
        },
      ],
    };
  },
  data() {
    return {
    };
  },
};
</script>

<style scoped>
.search-box {
  position: absolute;
  top: 20px;
  left: 20px;
}
.search-result-list {
  background: white;
}
.search-result-card{
  padding: 5px;
}
.search-result-card-company{
  color: gray;
  font-size: 75%;
}
</style>