<template>
  <div id="map"></div>
</template>

<script>
import L from 'leaflet';
import 'leaflet.markercluster';

let map;
const markerLayer = L.markerClusterGroup();
/* 紅色icon */
const redIcon = new L.Icon({
  iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png',
  shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41],
});

/* 藍色icon */
const blueIcon = new L.Icon({
  iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-blue.png',
  shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41],
});
/* 灰色icon */
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
      /* 起始中心位置 */
      centerCoord: [],
      parseData: [],
      showData: [],
    };
  },
  methods: {
    /* 呼叫父組件更新 showList方法 */
    updateListData() {
      this.$emit('updateShowList', this.showData);
    },
    /* 呼叫父組件更新 api整理後資料 */
    initData() {
      this.$emit('initList', this.showData);
    },
    /* 使用GPS定位功能 */
    getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.showPosition, this.showError);
      } else {
        this.showError();
        alert('定位可能出了一點小狀況');
      }
    },
    /* 若使用者同意定位，則初始化地圖，並將地圖focus至使用者的位置 */
    showPosition(position) {
    //   const latlon = `${position.coords.latitude},${position.coords.longitude}`;
      map = L.map('map').setView([position.coords.latitude, position.coords.longitude], 10);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '<a href="https://www.openstreetmap.org/">OSM</a>',
      }).addTo(map);
      this.centerCoord = [position.coords.latitude, position.coords.longitude];
    },
    /* 若使用者不同意定位，則初始化地圖，並將地圖focus至預設定義的位置 */
    showError() {
      this.centerCoord = [23.635915, 121.063619];
      map = L.map('map').setView([23.635915, 120.863619], 10);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '<a href="https://www.openstreetmap.org/">OSM</a>',
      }).addTo(map);
    },
    /* 將資料放入地圖 */
    importData() {
      /* 將api傳入資料變成js可用的形式 */
      this.maskData.forEach((element) => {
        this.parseData.push(JSON.parse(JSON.stringify(element)));
      });
      /* 將資料放入地圖 */
      this.parseData.forEach((ele) => {
        const thisLocat = ele.geometry.coordinates;
        let thisMarker;
        if (ele.properties.mask_adult === 0 && ele.properties.mask_child === 0) {
          // 成人口罩數量=0 且 兒童口罩數量=0 ==> 給予灰色圖標
          thisMarker = L.marker([thisLocat[1], thisLocat[0]], { icon: greyIcon });
        } else if (ele.properties.mask_adult < 10 && ele.properties.mask_child < 10) {
          // 成人口罩數量<10 且 兒童口罩數量<10 ==> 給予紅色圖標
          thisMarker = L.marker([thisLocat[1], thisLocat[0]], { icon: redIcon });
        } else {
          thisMarker = L.marker([thisLocat[1], thisLocat[0]], { icon: blueIcon });
        }
        /* 給予圖標點擊後跑出的小視窗要顯示的內容 */
        thisMarker.bindPopup(`<h2><a href='https://www.google.com.tw/maps/dir//${ele.properties.address}'>${ele.properties.name}</a></h2><h4>大人口罩:${ele.properties.mask_adult}</h4><h4>小孩口罩${ele.properties.mask_child}</h4>`).openPopup();
        markerLayer.addLayer(thisMarker);
      });
      map.addLayer(markerLayer);
      /* 更新資料 */
      this.showData = this.parseData;
      this.updateListData();
    },
    mapPanTo(newLocate) {
      /* 定位至傳入座標 */
      map.panTo(newLocate);
    },
  },
  beforeMount() {
    this.getLocation();
    if (typeof this.maskData[0] !== 'undefined') {
      //    若有資料，就放入地圖，並更新初始資料
      this.importData();
      this.initData();
    }
  },
  watch: {
    // 當api資料更新，放入地圖，並更新初始資料
    maskData(newVal) {
      if (typeof newVal[0] !== 'undefined') {
        this.importData();
        this.initData();
      }
    },
    /* 當該資料有變動，表示需要定位至新座標 */
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
