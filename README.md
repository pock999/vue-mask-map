# vue-mask-map

## Demo
https://pock999.github.io/vue-mask-map/

## Introduction
口罩地圖

參考影片:

 - [六角學院 - Leaflet + OpenStreetMap 地圖應用開發](https://www.youtube.com/watch?v=pUizu62dlnY)

 - [六角學院 - 示範：使用 Vuejs 結合 Open Street Map 製作口罩地圖](https://www.youtube.com/watch?v=7CXnNMVMXeo)

使用API :
https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json

使用圖標:
https://github.com/pointhi/leaflet-color-markers


### Screenshots
![Demo](https://github.com/pock999/vue-mask-map/blob/master/demo/demo.gif?raw=true)


<p align="center">
    <img width="350" height="600" src="https://github.com/pock999/vue-mask-map/blob/master/demo/moblie_demo.gif?raw=true">
</p>

### Function description

1 獲取資料並顯示至地圖上

2 可以**選擇縣市、鄉鎮市區**並獲取資料顯示在左側清單(**這個部分手機版隱藏**)

3 選擇縣市、鄉鎮市區後，地圖定位至該地區

4 在一開始可以**依據使用者位置定位**

### Additional footnote

 - 使用Ajax(**axios、VueAxios**)獲取資料

 - 在 _Vue 組件溝通_ 部分，有用到將**Props父傳子**，還有使用到**Emit子傳父**

 - 為了在資料更新後馬上獲得反應，所以本次有使用到 _Vue watch(監聽)_ 的功能

 - 本專案有特別用到 _Vue 生命週期概念_ 
   - 選在 `created` 階段載入Ajax，因為該階段資料 `$data` 已可取得
   - 子組件選在 `beforeMount`階段將資料更新，因為該階段資料變化時被呼叫，還不會描繪 View，目的是為了第一次顯示時就能夠讓資料到位

 - 使用**bootstrap**