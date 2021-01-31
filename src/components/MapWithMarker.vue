<template>
  <div :class="{ [`${uuid}`]: true }" >
    <div v-if="loading">
      <slot name="loading">
        <p>Loading</p>
      </slot>
    </div>
  </div>
</template>

<script>
let uuid = 0;
let mapboxglCache;

export default {
  name: "SkMapWithMarker",
  props: {
    accessToken: {
      type: String,
      required: true
    },
    markers: {
      type: Array,
      default: () => [],
    },
    mapboxStyle: {
      type: String,
      default: () => "mapbox://styles/mapbox/streets-v11"
    },
    mapboxPromise: {
      type: Function,
      required: true,
    }
  },
  data: () => {
    const localUUID = uuid;
    uuid++;
    return {
      uuid: `SkMapWithMarkerClassId-${localUUID}`,
      markerArray: [],
      loading: false
    };
  },
  mounted() {
    this.loadMapbox();
  },
  beforeDestroy() {
    this.destroy();
  },
  updated() {
    this.$nextTick(() => {
      this.syncMarkers();
    });
  },
  methods: {
    async loadMapbox() {
      if (!mapboxglCache) this.loading = true;
      const mapboxgl = mapboxglCache ? mapboxglCache : await this.mapboxPromise();
      mapboxglCache = mapboxgl;

      this.loading = false;

      const container = document.querySelector(`.${this.uuid}`);

      const map = new mapboxgl.Map({
        container,
        style: this.mapboxStyle,
        accessToken: this.accessToken
      });
      this.map = map;
      this.mapboxgl = mapboxgl;
      this.syncMarkers();
    },
    destroy() {
      if (!this.map) return;
      if (!this.mapboxgl) return;
      this.map.remove();
    },
    syncMarkers() {
      if (!this.map) return;
      if (!this.mapboxgl) return;
      let localMarkerArray = this.markerArray;
      const localMarkerData = this.markers;

      for (const marker of localMarkerArray) {
        marker.remove();
      }

      localMarkerArray = [];

      const bounds = new this.mapboxgl.LngLatBounds();

      for (const markerData of localMarkerData) {
        if (!markerData?.coordinates) continue;
        bounds.extend(markerData.coordinates);
        const marker = new this.mapboxgl.Marker()
            .setLngLat(markerData.coordinates)
            .addTo(this.map);

        if (markerData.title) {
          marker.setPopup(new this.mapboxgl.Popup().setHTML(`<h1>${markerData.title}</h1>`))
        }

        localMarkerArray.push(marker);
      }

      this.markerArray = Object.freeze(localMarkerArray);

      if (localMarkerArray.length > 1) {
        this.map.fitBounds(bounds, {
          padding: {top: 25, bottom:25, left: 25, right: 25}
        });
      } else if (localMarkerArray.length === 1) {
        this.map.flyTo({center: localMarkerArray[0].getLngLat(), zoom: 9});
      } else {
        this.map.flyTo({center: [10.018343, 51.133481], zoom: 4});
      }
    }
  },
  watch: {
    markers: {
      deep: true,
      handler() {
        this.syncMarkers();
      }
    }
  }
}
</script>

<style>
@import "~mapbox-gl/dist/mapbox-gl.css";
</style>
