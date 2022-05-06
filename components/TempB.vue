<template>
  <div id="sidebar">
    <div id="sidebar-icon" @click="isSideVisible = !isSideVisible">
      Sidebar Toggle
    </div>
    <div v-if="isSideVisible" id="sidebar-list">
      <div v-for="user in allMarkers" class="sidebar-list-card">
        <img :src="user.photoUrl" class="sidebar-list-card-photo" />
        {{ user.name }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "SideBar",
  data() {
    return {
      isSideVisible: false,
    };
  },
  props: ["allMarkers"],
};
</script>

<style>
#sidebar {
  position: absolute;
  top: 0;
  right: 0;
}
#sidebar-icon {
  margin: 20px;
  padding: 6px 0px 6px 12px;
  cursor: pointer;
  background: white;
  border-radius: 5px;
}
#sidebar-list {
  background: white;
  padding: 10px;
}
.sidebar-list-card {
  cursor: pointer;
  margin: 10px 5px;
  display: flex;
  align-items: center;
}
.sidebar-list-card-photo {
  margin-right: 10px;
  height: 35px;
  width: 35px;
  border-radius: 50%;
}
</style>