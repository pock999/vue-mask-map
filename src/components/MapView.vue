<template>
  <div id="map"></div>
</template>

<script>
import L from 'leaflet';

export default {
  name: 'MapView',
  data() {
    return {
      centerCoord: [],
    };
  },
  methods: {
    getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.showPosition, this.showError);
      } else {
        alert('定位至25.035915, 121.563619');
      }
      return this.showPosition();
    },
    showPosition(position) {
      const latlon = `${position.coords.latitude},${position.coords.longitude}`;
      // 回傳座標
      console.log(latlon);
      this.centerCoord = [position.coords.latitude, position.coords.longitude];
    },
    showError() {
      this.centerCoord = [25.035915, 121.563619];
    },
    displayMap() {},
  },
  mounted() {
    const map = L.map('map').setView([25.035915, 121.563619], 10);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '<a href="https://www.openstreetmap.org/">OSM</a>',
    }).addTo(map);
  },
};
</script>

<style>
  @import '../../node_modules/bootstrap/dist/css/bootstrap.css';
  @import '../../node_modules/leaflet/dist/leaflet.css';
  #map {
   height: 100%;
   width: 100%;
  }
</style>
