<template>
  <div id="app">
    <loading loader="bars"
        :active.sync="isLoading"
        :can-cancel="true"
        :is-full-page="false"></loading>
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-4">
          <div class="form-group">
            <select class="form-control form-control-lg" v-model="county">
              <option value=''>--請選擇縣市--</option>
              <option v-for="(item, index) in countyList" :key="index" :value="item">
                {{ item }}
              </option>
            </select>
            <select class="form-control mt-2" v-model="town">
              <option value=''>--請選擇行政區--</option>
              <option v-for="(item, index) in townList" :key="index" :value="item">
                {{ item }}
              </option>
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
          <MapView :mask-data = "apiData" @updateShowList="updateShowList"
          :pento-locate = "penToLocation"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import L from 'leaflet';
import VueLoading from 'vue-loading-overlay';
import MapView from './components/MapView.vue';

const BreakException = {};

export default {
  name: 'App',
  components: {
    MapView,
    loading: VueLoading,
  },
  data() {
    return {
      apiURL: 'https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json',
      apiData: [],
      showList: [],
      countyList: ['基隆市', '臺北市', '新北市', '連江縣', '宜蘭縣', '釣魚臺', '新竹市', '新竹縣', '桃園市', '苗栗縣', '臺中市', '彰化縣', '南投縣', '嘉義市', '嘉義縣', '雲林縣', '臺南市', '高雄市', '南海島', '澎湖縣', '金門縣', '屏東縣', '臺東縣', '花蓮縣'],
      townList: [],
      county: '',
      town: '',
      penToLocation: [],
      isLoading: true,
    };
  },
  watch: {
    county(val) {
      const ls = [];
      this.showList.forEach((item) => {
        if (item.properties.county === val) {
          ls.push(item.properties.town);
        }
      });
      this.townList = [...new Set(ls)];
    },
    town(val) {
      console.log(val);
      try {
        this.showList.forEach((item) => {
          if (item.properties.town === val) {
            this.penToLocation = [item.geometry.coordinates[1], item.geometry.coordinates[0]];
            console.log(this.penToLocation);
            throw BreakException;
          }
        });
      } catch (e) {
        console.log('find');
      }
    },
  },
  methods: {
    loadingAPI() {
      this.$http.get(this.apiURL).then((response) => {
        // console.log(response.data);
        this.apiData = response.data.features;
      }).then(() => {
        setTimeout(() => {
          this.isLoading = false;
        }, 2000);
      }).catch((e) => {
        console.log(e);
        alert('不好意思ajax出了問題!!');
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
  @import '../node_modules/vue-loading-overlay/dist/vue-loading.css';
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
