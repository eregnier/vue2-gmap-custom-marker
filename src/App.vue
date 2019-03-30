<template>
  <div id="app">
    <button
      class="button add-marker"
      :style="{'background-color': addMode ? '#66f13d' : '#3d66f1'}"
      @click="addMode=true"
    >{{ addMode ? 'Click on the map' : '+ Add marker' }}</button>
    <button
      class="button weather add-marker"
      :style="{'background-color': addWeather ? '#6f163d' : '#6d63f1'}"
      @click="addWeather=!addWeather"
    >{{ addWeather ? 'Weather marker' : '★ Icon Marker' }}</button>
    <table class="alignment">
      <tr>
        <td colspan="3">
          <center>Marker alignment : {{alignment}}</center>
        </td>
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
    <div class="animate-panel">
      <center>
        <label>
          <a href="https://daneden.github.io/animate.css/">Animate.css</a> type
        </label>
        <br>
        <br>
        <select v-model="selectedAnimation">
          <option v-for="animation in animations" :key="animation">{{animation}}</option>
        </select>
        <br>
        <br>
        <label>infinite animation</label>
        <button @click="infinite=!infinite">
          <span v-if="infinite">ON</span>
          <span v-else>OFF</span>
        </button>
      </center>
    </div>
    <div class="live-editor">
      <textarea v-model="html"></textarea>
    </div>
    <vue-gmap :center="markerCenter" :zoom="10" style="width: 100%" @click="onMapClick">
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
          <img
            :class="animation"
            v-else
            :title="JSON.stringify(marker)"
            class="icon-sm"
            :src="src"
            height="45"
          >
        </gmap-custom-marker>
      </cluster>
      <gmap-custom-marker alignment="bottomright" key="supermarker" :marker="markerCenter">
        <div class="card" @click="e => e.stopPropagation()">
          <center>
            <h3>This is a marker</h3>
            <p>Lat : {{markerCenter.lat}}, Lng : {{markerCenter.lng}}</p>
          </center>
          <img :class="animation" class="icon" :src="src" height="75">
          <div class="input-group">
            <center>
              <label>Set marker image urls</label>
            </center>
            <br>
            <input type="input" v-model="testText">
          </div>
        </div>
      </gmap-custom-marker>
      <gmap-custom-marker :z-index="zA" :marker="{lat: 50.4272265, lng: 2.95}">
        <div class="zindex zindex-a">
          <center>
            <p>z-index live edit A</p>
            <p>
              <b>{{zA}}</b>
            </p>
            <button @click="zA++">+</button>
            <button @click="zA--">-</button>
          </center>
        </div>
      </gmap-custom-marker>
      <gmap-custom-marker :z-index="zB" :marker="{lat: 50.4272265, lng: 2.80}">
        <div class="zindex zindex-b">
          <center>
            <p>z-index live edit B</p>
            <p>
              <b>{{zB}}</b>
            </p>
            <button @click="zB++">+</button>
            <button @click="zB--">-</button>
          </center>
        </div>
      </gmap-custom-marker>
      <gmap-custom-marker alignment="center" :marker="{lat: 50.7, lng: 3.8}">
        <div class="live-html" v-html="html"></div>
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
      html: "<center><h2>This is <u>live</u> html marker 🔥</h2></center>",
      infinite: true,
      selectedAnimation: "",
      zA: 50,
      zB: 51,
      testText: "",
      markerCenter: {
        lat: 50.6272265,
        lng: 3.0571581
      },
      addWeather: false,
      addMode: false,
      markers: [],
      ids: 0,
      alignment: "top",
      animations: require("./animations.json")
    };
  },
  computed: {
    animation() {
      const infinite = this.infinite ? "infinite" : "";
      return `animated ${infinite} ${this.selectedAnimation}`;
    },
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
@import "https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css";
html,
body,
#app,
.vue-map-container {
  height: 100%;
  padding: 0px;
  margin: 0px;
}
* {
  font-family: sans-serif;
  color: #444;
}
.live-html {
  padding: 5px;
  border: 1px solid #aaa;
  border-radius: 4px;
  box-shadow: 3px 3px 3px grey;
  min-height: 200px;
  min-width: 200px;
  background-color: #fafafa;
}
.zindex {
  padding: 5px;
  border: 1px solid #aaa;
  border-radius: 4px;
  box-shadow: 3px 3px 3px grey;
  min-height: 100px;
  min-width: 100px;
}
.zindex-a {
  background-color: #bbdddd;
}
.zindex-b {
  background-color: #ddbbdd;
}
.card {
  border-radius: 4px;
  background-color: #fafafa;
  padding: 15px;
  box-shadow: 3px 3px 3px grey;
  border: 1px solid #ccc;
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
  background-color: #dadafa;
}

.alignment {
  right: 10px;
  position: absolute;
  z-index: 1000;
  background-color: white;
  padding: 4px;
  top: 15px;
  border: 1px solid #ccc;
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
  top: 70px;
}

.icon-sm {
  padding: 3px;
  border-radius: 4px;
}
.animate-panel {
  padding: 7px;
  position: absolute;
  right: 10px;
  top: 150px;
  border-radius: 7px;
  background-color: #fafafa;
  height: 100px;
  width: 250px;
  z-index: 1000;
  border: 1px solid #ccc;
}

.live-editor {
  position: absolute;
  right: 10px;
  top: 270px;
  z-index: 1000;
  height: 100px;
  width: 250px;
  border: 1px solid #ccc;
}
.live-editor textarea {
  width: 100%;
  height: 100%;
}

.add-marker {
  padding: 8px;
  font-size: 1.3em;
}
</style>
