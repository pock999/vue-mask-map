<template>
  <div id="map"></div>
</template>

<script>
import L from 'leaflet';

export default {
  name: 'MapView',
  props: ['maskData'],
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
        alert('出了一點小狀況，請稍後再試');
      }
    },
    showPosition(position) {
    //   const latlon = `${position.coords.latitude},${position.coords.longitude}`;
      const map = L.map('map').setView([position.coords.latitude, position.coords.longitude], 10);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '<a href="https://www.openstreetmap.org/">OSM</a>',
      }).addTo(map);
      this.centerCoord = [position.coords.latitude, position.coords.longitude];
    },
    showError() {
      this.centerCoord = [25.035915, 121.563619];
      const map = L.map('map').setView([25.035915, 121.563619], 10);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '<a href="https://www.openstreetmap.org/">OSM</a>',
      }).addTo(map);
    },
    displayMap() {},
  },
  beforeMount() {
    this.getLocation();
    if (typeof this.maskData[0] !== 'undefined') {
      //    若有資料，就放入地圖
    }
  },
  watch: {
    maskData() {
      // 資料更新，放入地圖
    },
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
