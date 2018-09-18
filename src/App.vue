<template>
  <div id="app">
    <vue-gmap :center="{lat: 50.6272265, lng: 3.0571581}" :zoom="12" style="width: 100%; height: 600px" @click="onMapClick">
      <gmap-custom-marker
        key="supermarker"
        :marker="markerCenter">
        <div class="card" @click="e => e.stopPropagation()">
          <center>
            <h1>Click on the map to move me around</h1>
            <p>{{markerCenter}}</p>
          </center>
          <img class="icon" :src="testText ? testText : 'https://vuejs.org/images/logo.png'" height="75"/>
          <div class="input-group">
            <label>Image url </label>
            <input type="input" v-model="testText"/>
          </div>
          <p>{{testText}}</p>
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
      }
    }
  },
  methods: {
    onMapClick (event) {
      this.markerCenter = {
        latitude: event.latLng.lat(),
        longitude: event.latLng.lng()
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
}
.input-group {
  float: right;
  margin-top:25px;
  margin-right:25px;
}
.icon {
  margin-left: 15px;
}
</style>
