<template>
  <div>
    <h1>Hello! {{ name }}</h1>
    <div>
      <span>
        <h3>Slide the slider for select the date</h3>
      </span>
      <vueSlider v-bind="sliderOption" v-model="value" @change="updatedMap"></vueSlider>
      <h4>Your Selected date is: {{ value}} of march</h4>
    </div>
    <div>
      <l-map ref="mymap" @ready="initmap" :zoom = "zoom" :center="center" style="height:500px">
        <l-tile-layer :url="url"></l-tile-layer>
        <l-geo-json :geojson="geojsondata" :options="options"></l-geo-json>
      </l-map>
    </div>
  </div>
</template>

<script>
import L from "leaflet";
import data from "./ward_covid_data";
import { LMap, LTileLayer, LGeoJson } from "vue2-leaflet";
import VueSlider from "vue-slider-component";
export default {
  components: {
    LMap,
    LTileLayer,
    LGeoJson,
    VueSlider
  },
  data() {
    return {
      name: "LeafLet",
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      zoom: 15,
      center: [18.5204, 73.8567],
      geojsondata: data,
      dates: ["08", "09", "10", "11", "12", "13", "14", "15", "16", "17", "18"],
      value: null,
      fillColor: "#e4ce7f",
      sliderOption: {
        dotSize: 15,
        width: "900px",
        height: 5,
        direction: "ltr",
        data: this.dates,
        min: 8,
        max: 18,
        interval: 1
      }
    };
  },
  methods: {
    initmap() {
      this.map = this.$refs.mymap.mapObject;
    },
    getColor(d) {
      //console.log(d);
      return d > 21
        ? "#f03b20"
        : d > 18
        ? "#f03b20"
        : d > 12
        ? "#f03b20"
        : d > 9
        ? "#fdbb84"
        : d > 6
        ? "#fdbb84"
        : d > 3
        ? "#fdbb84"
        : d > 1
        ? "#31a354"
        : "#2ca25f";
    },
    updatedMap(value) {
      this.value = value;
      let option = {
        style: this.styleFunction
      };
      L.geoJSON(this.geojsondata, option).addTo(this.map);
    }
  },
  computed: {
    options() {
      return {
        style: this.styleFunction
      };
    },
    styleFunction() {
      let vm = this;
      return feature => {
        return {
          fillColor: this.getColor(feature.properties["covid_04_" + vm.value]),
          weight: 2,
          opacity: 1,
          color: "white",
          dashArray: "3",
          fillOpacity: 0.7
        };
      };
    }
  }
};
</script>