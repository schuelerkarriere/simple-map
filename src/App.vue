<template>
  <div id="app">
    <sk-map-with-marker class="map" :access-token="accessToken" :markers="markers"></sk-map-with-marker>
  </div>
</template>

<script>

import SkMapWithMarker from "@/components/MapWithMarker";
export default {
  name: 'App',
  data: () => ({
    markers: [
      {
        coordinates: [9.555210, 52.323471],
        title: "Test"
      },
      {
        coordinates: [9.746700, 52.361138],
        title: "Test 2"
      }
    ],
    accessToken: process.env.VUE_APP_MAPBOX_ACCESS_TOKEN
  }),
  components: {
    SkMapWithMarker
  },
  mounted() {
    setTimeout(() => {
      console.log('Shift');
      this.markers.shift();
      this.markers.shift();
    }, 2000);
  }
}
</script>

<style>
#app {
  height: 100%;
}
.map {
  min-height: 500px;
}
</style>
