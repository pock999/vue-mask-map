<template>
  <div id="map"></div>
</template>

<script>
import L from 'leaflet';
import 'leaflet.markercluster';

let map;
const markerLayer = L.markerClusterGroup();
const redIcon = new L.Icon({
  iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png',
  shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41],
});

const blueIcon = new L.Icon({
  iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-blue.png',
  shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41],
});

const greyIcon = new L.Icon({
  iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-grey.png',
  shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41],
});

export default {
  name: 'MapView',
  props: ['maskData', 'pentoLocate'],
  data() {
    return {
      centerCoord: [],
      parseData: [],
      showData: [],
    };
  },
  methods: {
    updateListData() {
      this.$emit('updateShowList', this.showData);
    },
    getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.showPosition, this.showError);
      } else {
        alert('出了一點小狀況，請稍後再試');
      }
    },
    showPosition(position) {
    //   const latlon = `${position.coords.latitude},${position.coords.longitude}`;
      map = L.map('map').setView([position.coords.latitude, position.coords.longitude], 10);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '<a href="https://www.openstreetmap.org/">OSM</a>',
      }).addTo(map);
      this.centerCoord = [position.coords.latitude, position.coords.longitude];
    },
    showError() {
      this.centerCoord = [23.635915, 121.063619];
      map = L.map('map').setView([23.635915, 120.863619], 10);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '<a href="https://www.openstreetmap.org/">OSM</a>',
      }).addTo(map);
    },
    importDate() {
      this.maskData.forEach((element) => {
        this.parseData.push(JSON.parse(JSON.stringify(element)));
      });
      this.parseData.forEach((ele) => {
        const thisLocat = ele.geometry.coordinates;
        let thisMarker;
        if (ele.properties.mask_adult === 0 && ele.properties.mask_child === 0) {
          thisMarker = L.marker([thisLocat[1], thisLocat[0]], { icon: greyIcon });
        } else if (ele.properties.mask_adult < 10 && ele.properties.mask_child < 10) {
          thisMarker = L.marker([thisLocat[1], thisLocat[0]], { icon: redIcon });
        } else {
          thisMarker = L.marker([thisLocat[1], thisLocat[0]], { icon: blueIcon });
        }
        thisMarker.bindPopup(`<h2>${ele.properties.name}</h2><h4>大人口罩:${ele.properties.mask_adult}</h4><h4>小孩口罩${ele.properties.mask_child}</h4>`).openPopup();
        markerLayer.addLayer(thisMarker);
      });
      map.addLayer(markerLayer);
      this.showData = this.parseData;
      this.updateListData();
    },
    mapPanTo(newLocate) {
      map.panTo(newLocate);
    },
  },
  beforeMount() {
    this.getLocation();
    if (typeof this.maskData[0] !== 'undefined') {
      //    若有資料，就放入地圖
      this.importDate();
    }
  },
  watch: {
    maskData(newVal) {
      // 資料更新，放入地圖
      if (typeof newVal[0] !== 'undefined') {
        this.importDate();
      }
    },
    pentoLocate(newLocate) {
      this.mapPanTo(newLocate);
    },
  },
};
</script>

<style>
  @import '../../node_modules/bootstrap/dist/css/bootstrap.css';
  @import '../../node_modules/leaflet/dist/leaflet.css';
  @import '../../node_modules/leaflet.markercluster/dist/MarkerCluster.css';

  #map {
   height: 100%;
   width: 100%;
  }
  .marker-cluster{
    background-color: #ffa826b9;
    border-radius:50%;
  }
  .marker-cluster div{
    text-align: center;
    line-height:40px;
  }
</style>
