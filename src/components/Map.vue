<template>
  <div>
    <div class="coordinates-container">
      <div>
        <h2>Your coordinates:</h2>
        <p>
          {{ myCoordinates.lat.toFixed(4) }} Latitude, {{ myCoordinates.lng.toFixed(4) }} Longitude
        </p>
      </div>
      <div>
        <h2>Map coordinates:</h2>
        <p>
          {{ mapCoordinates.lat }} Latitude, {{ mapCoordinates.lng }} Longitude
        </p>
      </div>
    </div>
    <GmapMap
      :center="myCoordinates"
      :zoom="zoom"
      class="gmap-map"
      ref="mapRef"
      @dragend="handleDrag"
    >
      <GmapMarker
        ref="myMarker"
        :position="
          google && new google.maps.LatLng(myCoordinates.lat, myCoordinates.lng)
        "
      />
    </GmapMap>
  </div>
</template>

<script>
import { gmapApi } from "vue2-google-maps";
export default {
  data() {
    return {
      map: null,
      myCoordinates: {
        lat: 0,
        lng: 0,
      },
      zoom: 14,
    };
  },
  created() {
    if (localStorage.center) {
      this.myCoordinates = JSON.parse(localStorage.center);
    } else {
      // getting users coordinates from browser request
      this.$getLocation({
        enableHighAccuracy: true
      })
        .then((coordinates) => {
          this.myCoordinates = coordinates;
        })
        .catch((err) => alert(err));
    }
    if (localStorage.zoom) {
      this.zoom = parseInt(localStorage.zoom);
    }
  },
  mounted() {
    // adding the map to a data object
    return this.$refs.mapRef.$mapPromise.then((map) => (this.map = map));
  },
  methods: {
    handleDrag() {
      let center = {
        lat: this.map.getCenter().lat(),
        lng: this.map.getCenter().lng(),
      };
      let zoom = this.map.getZoom();

      localStorage.center = JSON.stringify(this.map.getCenter());
      localStorage.zoom = zoom;
    },
  },
  computed: {
    google: gmapApi,
    mapCoordinates() {
      if (!this.map) {
        return {
          lat: 0,
          lng: 0,
        };
      }
      return {
        lat: this.map
          .getCenter()
          .lat()
          .toFixed(4),
        lng: this.map
          .getCenter()
          .lng()
          .toFixed(4),
      };
    },
  },
};
</script>

<style scoped>
h2 {
  font-size: 30px;
}
.coordinates-container {
  display: flex;
  align-items: center;
  justify-content: space-around;
  border: 10px solid black;
  max-width: 900px;
  margin: 0 auto;
}

.gmap-map {
  width: 900px;
  height: 480px;
  margin: 0 auto 32px auto;
  border: 10px solid black;
  border-top: none;
}
</style>
