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
      <vueSlider  v-bind="sliderOption1" v-model="value" @change="updatedMapDate"></vueSlider>
    </div>
    <div class="result">
      <h4>Your Selected date is: {{ monthValue }}-{{ value }}</h4>
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
// import data from "./ward_covid_data";
import data from"./covid";
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
      dates: ['01','02','03','04','05','06','07',"08", "09", "10", "11", "12", "13", "14", "15", "16", "17", "18","19",'20','21','22','23','24','25','26','27','28','29','30'],
      months: ['04','05'],
      value: null,
      monthValue: null,
      fillColor: "#e4ce7f",
      sliderOption1: {
        dotSize: 15,
        width: "900px",
        height: 5,
        direction: "ltr",
        data: this.dates,
        min: 1,
        max: 30,
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
        max: 7,
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
      return d > 75
        ? "#f03b20"
        : d > 65
        ? "#f03b20"
        : d > 45
        ? "#f03b20"
        : d > 35
        ? "#fdbb84"
        : d > 25
        ? "#fdbb84"
        : d > 15
        ? "#fdbb84"
        : d > 5
        ? "#31a354"
        : "#31a354";
    },
    updatedMapDate(value) {
      //this.dateValue = dateValue;
      if (
        value == 1 ||
        value == 2 ||
        value == 3 ||
        value == 4 ||
        value == 5 ||
        value == 6 ||
        value == 7 ||
        value == 8 ||
        value == 9
      )  {
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
      if(monthValue == 4){
        this.sliderOption1.max = 30
      }
      if(monthValue == 5){
        this.sliderOption1.max = 5
      }
      let option = {
        style: this.styleFunction
      };
      console.log("monthValue", this.monthValue);
      console.log("max", this.sliderOption1.max);
      L.geoJSON(this.geojsondata, option).addTo(this.map);
    }
  },
  computed: {
    options() {
      return {
        style: this.styleFunction,
        onEachFeature: this.onEachFeatureFunction
      };
    },
    styleFunction() {
      let vm = this;
      return feature => {
        return {
          fillColor: this.getColor(
            feature.properties["covid_2020-" + vm.monthValue + "-" + vm.value]
          ),
          weight: 2,
          opacity: 1,
          color: "white",
          dashArray: "3",
          fillOpacity: 0.5
        };
      };
    },
    onEachFeatureFunction() {
      let vmq = this;
      return (feature, layer) => {
        layer.bindTooltip(
          "<div>Ward Name:" +
            feature.properties.ward_name +
            "</div><div>no of case: " +
            feature.properties["covid_2020-" + vmq.monthValue + "-" + vmq.value] +
            "</div>",
          { permanent: false, sticky: true }
        );
      };
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