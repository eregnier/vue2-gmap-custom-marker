<template>
  <div class="weather-card" v-if="data">
    <label>
      <b>{{data.name}}</b>
    </label>
    <br>
    <img :src="icon">
    <p>{{data.weather[0].description}}</p>
    <p>
      <i>{{data.main.temp.toFixed(0)}}°C</i>
    </p>
  </div>
</template>
<script>
import { get } from "axios";

export default {
  props: {
    coords: {
      type: Object,
      default: undefined
    }
  },
  data() {
    return {
      data: undefined
    };
  },
  created() {
    get(
      "https://api.openweathermap.org/data/2.5/weather?lat=" +
        this.coords.latitude +
        "&lon=" +
        this.coords.longitude +
        "&APPID=59b0d37a8bb62db251652e421b20342a&units=metric"
    ).then(response => {
      this.data = response.data;
    });
  },
  computed: {
    icon() {
      return (
        "http://openweathermap.org/img/w/" + this.data.weather[0].icon + ".png"
      );
    }
  }
};
</script>
<style>
.weather-card {
  background-color: #afccbc;
  border: 1px dashed #555;
  padding: 4px;
  border-radius: 4px;
  text-align: center;
}
</style>
