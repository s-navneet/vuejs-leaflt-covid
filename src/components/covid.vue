<template>
  <div class="maincontainer">
    <h1>Hello! {{ name }}</h1>
    <div class="heading">
      <h3>Slide the slider for select the date</h3>
    </div>
    <div class="monthslider">
      <span>Month</span>
      <vueSlider v-bind="sliderOption2" v-model="monthValue"  @change="updatedMapMonth"></vueSlider>
    </div>
    <div class="dateslider">
      <span>Date</span>
      <vueSlider v-bind="sliderOption1" v-model="value" @change="updatedMapDate"></vueSlider>
    </div>
    <div class="result">
      <h4>Your Selected date is: {{ value}}-{{ monthValue }}</h4>
    </div>
    <div class="map">
      <l-map ref="mymap" @ready="initmap" :zoom="zoom" :center="center" style="height:500px">
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
      zoom: 8,
      center: [18.5204, 73.8567],
      geojsondata: data,
      dates: ["08", "09", "10", "11", "12", "13", "14", "15", "16", "17", "18"],
      months: ['04','05','06'],
      value: null,
      monthValue: null,
      fillColor: "#e4ce7f",
      sliderOption1: {
        dotSize: 15,
        width: "900px",
        height: 5,
        direction: "ltr",
        data: this.dates,
        min: 8,
        max: 18,
        interval: 1,
        marks: true
      },
      sliderOption2: {
        dotSize: 15,
        width: "900px",
        height: 5,
        direction: "ltr",
        data: this.months,
        min: 4,
        max: 6,
        interval: 1,
        marks: true,
        
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
    updatedMapDate(value) {
      //this.dateValue = dateValue;
      if (value == 8 || value == 9) {
        this.value = "0" + value;
      } else {
        this.value = value;
      }
      console.log("value", this.value);
      let option = {
        style: this.styleFunction,
        onEachFeature: this.onEachFeatureFunction
      };
      L.geoJSON(this.geojsondata, option).addTo(this.map);
    },
    updatedMapMonth(monthValue) {
      if (
        monthValue == 1 ||
        monthValue == 2 ||
        monthValue == 3 ||
        monthValue == 4 ||
        monthValue == 5 ||
        monthValue == 6 ||
        monthValue == 7 ||
        monthValue == 8 ||
        monthValue == 9
      ) {
        this.monthValue = "0" + monthValue;
      } else {
        this.monthValue = monthValue;
      }
      let option = {
        style: this.styleFunction
      };
      console.log("monthValue", this.monthValue);
      L.geoJSON(this.geojsondata, option).addTo(this.map);
    }
  },
  computed: {
    options() {
      return {
        style: this.styleFunction
        //onEachFeature: onEachFeatureFunction
      };
    },
    styleFunction() {
      let vm = this;
      return feature => {
        return {
          fillColor: this.getColor(
            feature.properties["covid_" + vm.monthValue + "_" + vm.value]
          ),
          weight: 2,
          opacity: 1,
          color: "white",
          dashArray: "3",
          fillOpacity: 0.7
        };
      };
    },
    onEachFeatureFunction() {
      return (feature, layer) => {};
    }
  }
};
</script>
<style scoped>
.monthslider {
  top: 20px;
  height: 50px;
  position: relative;
}
.dateslider {
  top: 30px;
  height: 50px;
  position: relative;
}
.result {
  top: 30px;
  height: 50px;
  position: relative;
}
</style>