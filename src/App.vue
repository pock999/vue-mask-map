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
          <div class="list-group">
            <a href="#" class="list-group-item list-group-item-action flex-column
            align-items-start">
              <div class="d-flex w-100 justify-content-between">
                <h5 class="mb-1">藥局名稱</h5>
                <small>行政區 村里</small>
              </div>
              <p class="mb-1">地址</p>
              <p class="mb-1">看診時間</p>
              <ul style="padding-left:0;">
                <li style="list-style-type:none; text-align:left;">大人口罩:</li>
                <li style="list-style-type:none; text-align:left;">小孩口罩:</li>
              </ul>
              <small>電話</small>
            </a>
            <!--  -->
            <a href="#" class="list-group-item list-group-item-action flex-column
            align-items-start">
              <div class="d-flex w-100 justify-content-between">
                <h5 class="mb-1">藥局名稱</h5>
                <small>行政區 村里</small>
              </div>
              <p class="mb-1">地址</p>
              <p class="mb-1">看診時間</p>
              <ul style="padding-left:0;">
                <li style="list-style-type:none; text-align:left;">大人口罩:</li>
                <li style="list-style-type:none; text-align:left;">小孩口罩:</li>
              </ul>
              <small>電話</small>
            </a>
          </div>
        </div>
        <div class="col-md-8">map</div>
      </div>
    </div>
    <!-- <MapView/> -->
  </div>
</template>

<script>
// import L from 'leaflet';
// import MapView from './components/MapView.vue';

export default {
  name: 'App',
  components: {
    // MapView,
  },
};
</script>

<style>
@import '../node_modules/bootstrap/dist/css/bootstrap.css';
@import '../node_modules/leaflet/dist/leaflet.css';
*{
  font-family: '微軟正黑體', Courier, monospace;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
