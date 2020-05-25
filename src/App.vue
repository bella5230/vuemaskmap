<template>
  <div id="app">
    <!-- <Titlecomponent :title="title"/>-->
    <!-- <inputcomponent :text="title" @textChage="changeText"/> -->
    <!-- <inputcomponent :text.sync="title"/>
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>  -->
    <div class="navbar navbar-dark bg-dark shadow-sm">
      <div class="d-flex justify-content-start">
        <a href="#" class="navbar-brand d-flex align-items-center">
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" aria-hidden="true" class="mr-2" viewBox="0 0 24 24" focusable="false"><path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z"></path><circle cx="12" cy="13" r="4"></circle></svg>
          <strong>Mask Map</strong>
        </a>
      </div>
    </div>

    <div class="home row no-gutters">
      <div class="col-sm-3">
        <div class="toolbox">
          <div class="sticky-top bg-white shadow-sm p-2">
            <div class="mt-2 form-group d-flex">
              <label for="cityName" class="mr-2 ml-2 col-form-label text-right">縣市：</label>
              <div class="flex-fill">
                <select id="cityName" class="mt-1 form-control-sm" v-model="select.city">
                <option value="">-- Select One --</option>
                <option :value="c.CityName" v-for="c in cityName" :key="c.CityName">
                  {{c.CityName}}
                </option>
                </select>
              </div>
            </div>
             <div class="form-group d-flex">
              <label for="area" class="mr-2 ml-2 col-form-label text-right">地區：</label>
                <div class="flex-fill">
                  <select id="area" class="mt-1 form-control-sm" v-if="select.city.length"
                    v-model="select.area" @change="updateSelect">
                  <option value="">-- Select One --</option>
                  <option :value="a.AreaName"
                    v-for="a in cityName.find((city) => city.CityName === select.city).AreaList"
                    :key="a.AreaName">
                    {{ a.AreaName }}
                  </option>
                  </select>
                </div>
              </div>
              
              </div>
          <ul class="list-group">
            <template v-for="(item, key) in data">
              <a class="list-group-item text-left" :key="key" v-if="item.properties.county === select.city
                      && item.properties.town === select.area"
                    :class="{ 'highlight': item.properties.mask_adult || item.properties.mask_child}"
                    @click="penTo(item)">
              <h6><strong class="mb-1" >{{item.properties.name}}</strong></h6>
              <strong class="mb-1 mr-1 btn btn-primary btn-sm">成人口罩：{{ item.properties.mask_adult}}</strong>
              <strong class="mb-1 btn btn-secondary btn-sm">兒童口罩：{{ item.properties.mask_child}}</strong>
              
              <p class="mb-0 small text-muted">地址：<a href="https://www.google.com.tw/maps/place/..."
              target="_blank" title="Google Map">
              {{ item.properties.address }}</a>
              </p>
              </a>
            </template>
          </ul>
        </div>
      </div>
      <div class="col-sm-9">
        <div id="map"></div>
      </div>
    </div>
  </div>
</template>

<script>
import L from 'leaflet';
import cityName from './assets/cityName.json';
let osmMap={};
// console.log(L);
// import HelloWorld from './components/HelloWorld.vue'
// import Titlecomponent from './components/Title.vue'
// import inputcomponent from './components/inputcomponents/index.vue'

const iconsConfig = {
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41],
};
const icons = {
  green: new L.Icon({
    iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-green.png',
    ...iconsConfig,
  }),
  grey: new L.Icon({
    iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-grey.png',
    ...iconsConfig,
  }),
};
const osm = {
  addMapMarker(x, y, item) {
    const icon = item.mask_adult || item.mask_child ? icons.green : icons.grey;
    L.marker([y, x], {
      icon,
    }).addTo(osmMap).bindPopup(`<strong>${item.name}</strong> <br>
    口罩剩餘：<strong>成人 - ${item.mask_adult ? `${item.mask_adult} 個` : '未取得資料'}/ 兒童 - ${item.mask_child ? `${item.mask_child} 個` : '未取得資料'}</strong><br>
    地址: <a href="https://www.google.com.tw/maps/place/${item.address}" target="_blank">${item.address}</a><br>
    電話: ${item.phone}<br>
    <small>最後更新時間: ${item.updated}</small>`);
  },
  removeMapMarker() {
    osmMap.eachLayer((layer) => {
      if (layer instanceof L.Marker) {
        osmMap.removeLayer(layer);
      }
    });
  },
  penTo(x, y, item) {
    const icon = item.mask_adult || item.mask_child ? icons.green : icons.grey;
    osmMap.panTo(new L.LatLng(y, x));
    L.marker([y, x], {
      icon,
    }).addTo(osmMap).bindPopup(`<strong>${item.name}</strong> <br>
    口罩剩餘：<strong>成人 - ${item.mask_adult ? `${item.mask_adult} 個` : '未取得資料'}/ 兒童 - ${item.mask_child ? `${item.mask_child} 個` : '未取得資料'}</strong><br>
    地址: <a href="https://www.google.com.tw/maps/place/${item.address}" target="_blank">${item.address}</a><br>
    電話: ${item.phone}<br>
    <small>最後更新時間: ${item.updated}</small>`).openPopup();
  },
};
export default {
  name: 'App',
  data:() =>({
    cityName,
    data:[],
    osmMap: {},
    select:{
      city:'臺北市',
      area: '大安區',
    },
  }),
  // data(){
  //   return{
  //     title : "feefw"
  //   }
  // },
  // components: {
  //   // HelloWorld,
  //   // Titlecomponent,
  //   // inputcomponent
  // },
  computed: {
    pharmacies() {
      return this.data.filter((pharmacy) => {
        if (!this.select.area) {
          return pharmacy.properties.county === this.select.city;
        }
        return pharmacy.properties.town === this.select.area;
      });
    },
  },
  watch: {
    pharmacies(value) {
      console.log(value);
      this.updateMarker();
    },
  },
  methods:{
    updateMarker(){
      // clear markers
      osmMap.eachLayer((layer) => {
        if (layer instanceof L.Marker) {
          osmMap.removeLayer(layer);
        }
      });

      this.pharmacies.forEach((pharmacy) => {
        // 透過藥局經緯度疊加標記
        L.marker([
          pharmacy.geometry.coordinates[1],
          pharmacy.geometry.coordinates[0],
        ]).addTo(osmMap).bindPopup(`<p><strong style="font-size: 20px;">${pharmacy.properties.name}</strong></p>
          <strong style="font-size: 16px; color: #d45345;">口罩剩餘：成人 - ${pharmacy.properties.mask_adult ? `${pharmacy.properties.mask_adult} 個` : '未取得資料'} / 兒童 - ${pharmacy.properties.mask_child ? `${pharmacy.properties.mask_child} 個` : '未取得資料'}</strong><br>
          地址: ${pharmacy.properties.address}<br>
          電話: ${pharmacy.properties.phone}<br>
          <small>最後更新時間: ${pharmacy.properties.updated}</small>`);
      });
      this.penTo(this.pharmacies[0]);
    },
    removeMarker() {
      osm.removeMapMarker();
    },
    penTo(pharmacy) {
      const { properties, geometry } = pharmacy;
      osm.penTo(geometry.coordinates[0], geometry.coordinates[1], properties);
    },
    updateSelect() {
      this.removeMarker();
      this.updateMarker();
      const pharmacy = this.data.find(item => item.properties.town === this.select.area);
      const { geometry, properties } = pharmacy;
      osm.penTo(geometry.coordinates[0], geometry.coordinates[1], properties);
    },
  },
  mounted(){
    osmMap = L.map('map',{
      center:[25.03,121.55],
      zoom:15
    });
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
      maxZoom: 20,
    }).addTo(osmMap);

    // L.marker([25.03,121.55]).addTo(osmMap);

    this.$http.get('https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json')
      .then((res) => {
        this.data = res.data.features;
        this.updateMarker();
      });
  },
  // ,
  // methods:{
  //   // changeText(val){
  //   //   this.title=val
  //   // }

  // }

};

</script>


<style lang="scss">
@import 'bootstrap/scss/bootstrap';
#map {
 height: 100vh;
}
.highlight {
 background: #F2F8FA;
}
.toolbox {
 height: 100vh;
 overflow-y: auto;
 a {
 cursor: pointer;
 }
 #mapid { height: 180px; }
}
</style>
