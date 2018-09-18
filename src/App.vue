<template>
  <div id="app">
    <button class="button" :style="{'background-color': addMode ? '#66f13d' : '#3d66f1'}" @click="addMode=true">{{ addMode ? 'Click on the map' : '+ Add marker' }}</button>
    <vue-gmap :center="{lat: 50.6272265, lng: 3.0571581}" :zoom="12" style="width: 100%; height: 600px" @click="onMapClick">
      <gmap-custom-marker
        v-for="(marker, i) in addMarkers"
        :key="i"
        :marker="marker">
          <img :title="JSON.stringify(marker)" class="icon-sm" :src="src" height="45"/>
      </gmap-custom-marker>
      <gmap-custom-marker
        key="supermarker"
        :marker="markerCenter">
        <div class="card" @click="e => e.stopPropagation()">
          <center>
            <h1>Click on the map to move me around</h1>
            <p>{{markerCenter}}</p>
          </center>
          <img class="icon" :src="src" height="75"/>
          <div class="input-group">
            <label>Image url </label>
            <input type="input" v-model="testText"/>
          </div>
        </div>
      </gmap-custom-marker>
    </vue-gmap>
  </div>
</template>

<script>
import {Map} from 'vue2-google-maps'
import GmapCustomMarker from 'vue2-gmap-custom-marker'
export default {
  name: 'app',
  components: {
    GmapCustomMarker,
    'vue-gmap': Map
  },
  data () {
    return {
      testText: '',
      markerCenter: {
        latitude: 50.6272265,
        longitude: 3.0571581
      },
      addMode: false,
      addMarkers: []
    }
  },
  computed: {
    src () {
      return this.testText ? this.testText : 'https://vuejs.org/images/logo.png'
    }
  },
  methods: {
    onMapClick (event) {
      if (this.addMode) {
        this.addMarkers.push({
          latitude: event.latLng.lat(),
          longitude: event.latLng.lng()
        })
        this.addMode = false;
      }
    }
  }
}
</script>

<style>
.card {
  background-color: #efefef;
  padding: 15px;
  border: 1px solid black;
  min-height: 188px;
}
.input-group {
  float: right;
  margin-top:25px;
  margin-right:25px;
}
.icon {
  margin-left: 15px;
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
.icon-sm {
  padding: 3px;
  background-color: #eee;
  border: 1px solid #555;
  border-radius: 4px;
}
</style>
