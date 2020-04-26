<template>
  <div id="app">
    <!-- loading組件 -->
    <loading loader="bars"
        :active.sync="isLoading"
        :can-cancel="true"
        :is-full-page="false"></loading>
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-4">
          <div class="form-group">
            <!-- 線是選單 -->
            <select class="form-control form-control-lg" v-model="county">
              <option value=''>--請選擇縣市--</option>
              <option v-for="(item, index) in countyList" :key="index" :value="item">
                {{ item }}
              </option>
            </select>
            <!-- 鄉鎮市區選單 -->
            <select class="form-control mt-2" v-model="town">
              <option value=''>--請選擇行政區--</option>
              <option v-for="(item, index) in townList" :key="index" :value="item">
                {{ item }}
              </option>
            </select>
          </div>
          <!-- 左側清單 -->
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
          <!-- 將apiData(api獲得的資料)傳入子組件的maskData，讓子組件整理成可用格式
              將penToLocation(地圖定位座標)傳入子組件，使其在地圖上定位
              updateShowList 更新 showList方法，由子組件發起
              initList 更新 api整理後資料，由子組件發起 -->
          <MapView :mask-data = "apiData" @updateShowList="updateShowList"
          :pento-locate = "penToLocation" @initList="initList"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import L from 'leaflet';
import VueLoading from 'vue-loading-overlay';
import MapView from './components/MapView.vue';

export default {
  name: 'App',
  components: {
    MapView,
    loading: VueLoading,
  },
  data() {
    return {
      apiURL: 'https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json',
      /* api獲得資料，不能直接使用(未整理) */
      apiData: [],
      /* api整理後的資料，可以直接使用，和apiData差別在這個可以直接使用 */
      initData: [],
      /* 顯示在左側清單上的資料，隨著所選縣市鄉鎮更改 */
      showList: [],
      /* 縣市列表 */
      countyList: ['基隆市', '臺北市', '新北市', '連江縣', '宜蘭縣', '新竹市', '新竹縣', '桃園市', '苗栗縣', '臺中市', '彰化縣', '南投縣', '嘉義市', '嘉義縣', '雲林縣', '臺南市', '高雄市', '南海島', '澎湖縣', '金門縣', '屏東縣', '臺東縣', '花蓮縣'],
      /* 鄉鎮市區列表 */
      townList: [],
      /* 表示下拉式所選之縣市值 */
      county: '',
      /* 表示下拉式所選之鄉鎮市區值 */
      town: '',
      /* 地圖定位座標 */
      penToLocation: [],
      /* 控制loading組件是否顯示 */
      isLoading: true,
    };
  },
  watch: {
    /* 監看選擇下拉式縣市值，若有改變，則在所有資料裡面撈出該縣市之所有資料的鄉鎮市區
        ，並去除重複更新至鄉鎮市區列表
    */
    county(val) {
      const ls = [];
      this.initData.forEach((item) => {
        if (item.properties.county === val) {
          ls.push(item.properties.town);
        }
      });
      this.townList = [...new Set(ls)];
    },
    /* 監看選擇下拉式鄉鎮市區值，若有改變，則在所有資料裡面撈出該縣市、鄉鎮市區的所有資料
        ，並更新至showList(顯示在左側清單上的資料，隨著所選縣市鄉鎮更改)
    */
    town(val) {
      console.log(val);
      const newShowList = [];
      try {
        this.initData.forEach((item) => {
          if (item.properties.town === val) {
            this.penToLocation = [item.geometry.coordinates[1], item.geometry.coordinates[0]];
            console.log(this.penToLocation);
          }
          if (item.properties.address.includes(this.county) && item.properties.town === val) {
            newShowList.push(item);
          }
        });
        this.updateShowList(newShowList);
      } catch (e) {
        console.log('錯誤');
      }
    },
  },
  methods: {
    /* 請求API */
    loadingAPI() {
      this.$http.get(this.apiURL).then((response) => {
        // console.log(response.data);
        this.apiData = response.data.features;
      }).then(() => {
        setTimeout(() => {
          this.isLoading = false;
        }, 5000);
      }).catch((e) => {
        console.log(e);
        alert('不好意思ajax出了問題!!!');
      });
    },
    /* 更新showList */
    updateShowList(showData) {
      this.showList = showData;
    },
    /* 更新initData(api整理後的資料) */
    initList(showData) {
      this.initData = showData;
    },
  },
  created() {
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
