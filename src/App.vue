<template>
  <div id="app">
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-4">
          <div class="form-group">
            <select class="form-control form-control-lg">
              <option>--請選擇縣市--</option>
            </select>
            <select class="form-control mt-2">
              <option>--請選擇行政區--</option>
            </select>
          </div>
          <div class="list-group d-none d-md-block">
            <a class="list-group-item list-group-item-action flex-column
            align-items-start" v-for="(item, index) in showList" :key="index">
              <div class="d-flex w-100 justify-content-between">
                <h5 class="mb-1">{{ item.properties.name }}</h5>
                <small>{{ item.properties.town }} {{ item.properties.cunli }}</small>
              </div>
              <p class="mb-1">{{ item.properties.address }}</p>
              <p class="mb-1">資料更新時間:{{ item.properties.updated }}</p>
              <ul style="padding-left:0;">
                <li style="list-style-type:none; text-align:left;">大人口罩:
                  {{ item.properties.mask_adult }}</li>
                <li style="list-style-type:none; text-align:left;">小孩口罩:
                  {{ item.properties.mask_child }}</li>
              </ul>
              <small>{{ item.properties.phone }}</small>
            </a>
            <!--  -->
          </div>
        </div>
        <div class="col-md-8 map-container">
          <MapView :mask-data = "apiData" @updateShowList="updateShowList"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import L from 'leaflet';
import MapView from './components/MapView.vue';

export default {
  name: 'App',
  components: {
    MapView,
  },
  data() {
    return {
      apiURL: 'https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json',
      apiData: [],
      showList: [],
    };
  },
  methods: {
    loadingAPI() {
      this.$http.get(this.apiURL).then((response) => {
        // console.log(response.data);
        this.apiData = response.data.features;
      });
    },
    updateShowList(showData) {
      this.showList = showData;
    },
  },
  beforeMount() {
    this.loadingAPI();
  },
};
</script>

<style>
  @import '../node_modules/bootstrap/dist/css/bootstrap.css';
  @import '../node_modules/leaflet/dist/leaflet.css';
  @import '../node_modules/leaflet.markercluster/dist/MarkerCluster.css';

  #app{
    margin-top: 20px;
  }
  *{
    font-family: '微軟正黑體', Courier, monospace;
  }
  @media screen and (max-width:768px){
    .map-container{
      height: 79vh;
    }
  }
  .list-group{
    height: 80vh;
    overflow-y: scroll;
  }
</style>
