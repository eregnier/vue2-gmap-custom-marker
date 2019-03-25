<template>
  <div id="app">
    <button
      class="button"
      :style="{'background-color': addMode ? '#66f13d' : '#3d66f1'}"
      @click="addMode=true"
    >{{ addMode ? 'Click on the map' : '+ Add marker' }}</button>
    <button
      class="button weather"
      :style="{'background-color': addWeather ? '#6f163d' : '#6d63f1'}"
      @click="addWeather=!addWeather"
    >{{ addWeather ? 'Weather marker' : 'Icon Marker' }}</button>
    <table class="alignment">
      <tr>
        <td colspan="3">Marker alignment : {{alignment}}</td>
      </tr>
      <tr>
        <td>
          <button @click="alignment='topleft'">topleft</button>
        </td>
        <td>
          <button @click="alignment='top'">top</button>
        </td>
        <td>
          <button @click="alignment='topright'">topright</button>
        </td>
      </tr>
      <tr>
        <td>
          <button @click="alignment='left'">left</button>
        </td>
        <td>
          <button @click="alignment='center'">center</button>
        </td>
        <td>
          <button @click="alignment='right'">right</button>
        </td>
      </tr>
      <tr>
        <td>
          <button @click="alignment='bottomleft'">bottomleft</button>
        </td>
        <td>
          <button @click="alignment='bottom'">bottom</button>
        </td>
        <td>
          <button @click="alignment='bottomright'">bottomright</button>
        </td>
      </tr>
    </table>
    <vue-gmap
      :center="{lat: 50.6272265, lng: 3.0571581}"
      :zoom="12"
      style="width: 100%; height: 600px"
      @click="onMapClick"
    >
      <cluster>
        <gmap-custom-marker
          v-for="(marker, i) in markers"
          :key="marker._id"
          :delayRepaint="marker.weather ? 250 : 0"
          :marker="marker"
          :alignment="marker.alignment"
          @click.native="deleteMarker(i)"
        >
          <weather v-if="marker.weather" :coords="marker"/>
          <img v-else :title="JSON.stringify(marker)" class="icon-sm" :src="src" height="45">
        </gmap-custom-marker>
      </cluster>
      <gmap-custom-marker alignment="bottomright" key="supermarker" :marker="markerCenter">
        <div class="card" @click="e => e.stopPropagation()">
          <small>This is a marker</small>
          <center>
            <p>{{markerCenter}}</p>
          </center>
          <img class="icon" :src="src" height="75">
          <div class="input-group">
            <label>Image url</label>
            <input type="input" v-model="testText">
          </div>
        </div>
      </gmap-custom-marker>
    </vue-gmap>
  </div>
</template>

<script>
import { Map } from "vue2-google-maps";
import GmapCustomMarker from "vue2-gmap-custom-marker";
import Weather from "./components/weather";
export default {
  name: "app",
  components: {
    GmapCustomMarker,
    "vue-gmap": Map,
    Weather
  },
  data() {
    return {
      mas: [
        {
          lat: 50.6272265,
          lng: 3.0571581
        },
        {
          lat: 50.6373265,
          lng: 3.0571581
        },
        {
          lat: 50.6474265,
          lng: 3.0571581
        },
        {
          lat: 50.6575265,
          lng: 3.0571581
        }
      ],
      testText: "",
      markerCenter: {
        lat: 50.6272265,
        lng: 3.0571581
      },
      addWeather: false,
      addMode: false,
      markers: [],
      ids: 0,
      alignment: "top"
    };
  },
  computed: {
    src() {
      return this.testText
        ? this.testText
        : "https://vuejs.org/images/logo.png";
    }
  },
  methods: {
    deleteMarker(i) {
      this.markers.splice(i, 1);
    },
    onMapClick(event) {
      if (this.addMode) {
        this.markers.push({
          _id: this.ids++,
          latitude: event.latLng.lat(),
          longitude: event.latLng.lng(),
          weather: this.addWeather,
          alignment: this.alignment
        });
        this.addMode = false;
      }
    }
  }
};
</script>

<style>
.card {
  background-color: #efefef;
  padding: 15px;
  border: 1px solid black;
  min-height: 110px;
}
.input-group {
  float: right;
  margin-top: 25px;
  margin-right: 25px;
}
.icon {
  margin-left: 15px;
}
.alignment button {
  color: #555;
  width: 80px;
  padding: 5px;
  border: 1px solid #aaa;
  border-radius: 5px;
}

.alignment {
  right: 10px;
  position: absolute;
  z-index: 1000;
  background-color: white;
  padding: 4px;
  top: 15px;
  border: 1px solid #555;
  border-radius: 5px;
}
.button {
  position: absolute;
  left: 50%;
  top: 15px;
  z-index: 1000;
  color: white;
  padding: 5px;
  border: 1px solid #eee;
  border-radius: 5px;
  margin-left: -12px;
}

.weather {
  top: 50px;
}

.icon-sm {
  padding: 3px;
  border-radius: 4px;
}
</style>
