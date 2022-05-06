<template>
  <div id="search">
    <input v-model="input" @input="searchUsers()" id="search-box" type="text" autocomplete="false"/>
    <div v-if="input.length > 0" id="search-results">
      <div v-for="user in showMarkers.slice(0,20)" class="search-result" :key="user.id">
        {{ user.fullName }}
      </div>
      <div
        v-if="showMarkers.length == 0 && input.length > 0"
        class="search-result"
      >
        No such user
      </div>
    </div>
  </div>
</template>

<script>

export default {
  fullName: "SearchBar",
  data() {
    return {
      input: "",
      lastSearch: "",
      showMarkers: [],
    };
  },
  props: ["allMarkers", "addMarkers"],
  methods: {
    searchUsers() {
      // Simple but Flaw in this approach
      //   this.showMarkers = [];
      //   this.allMarkers.map((marker) => {
      //     if (marker.fullName.toLowerCase().startsWith(this.input.toLowerCase()))
      //       this.showMarkers.push(marker);
      //   });
      //   this.$emit("updateMarkers", this.showMarkers);

      // Complex approach a lil better
      if (
        this.input.toLowerCase().startsWith(this.lastSearch.toLowerCase()) &&
        this.showMarkers.length !== 0
      ) {
        //--- Only run on already skimmed array but doesn't work if backspaced
        this.showMarkers = this.showMarkers.filter((marker) => {
          //   console.log("Skimmed exisiting marker");
          return marker.fullName.toLowerCase().startsWith(this.input.toLowerCase());
        });
      } else {
        this.showMarkers = this.allMarkers;
        this.showMarkers = this.showMarkers.filter((marker) => {
          //   console.log("Checked every marker");
          return marker.fullName.toLowerCase().startsWith(this.input.toLowerCase());
        });
      }
      this.lastSearch = this.input;
      this.$emit("updateMarkers", this.showMarkers.slice(0,20));
    },
  },
};
</script>

<style>
#search {
  position: absolute;
  top: 20px;
  left: 20px;
}
#search-box {
  border-radius: 5px;
  border: none;
  outline: none;
  border: 1px solid blueviolet;
  padding: 6px 0px 6px 12px;
}
#search-results {
  background: white;
  border-radius: 5px;
  border-bottom: 1px solid blueviolet;
}
.search-result {
  padding: 6px 0px 6px 12px;
}
</style>