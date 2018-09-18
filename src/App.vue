<template>
  <div id="app">
    <vue-gmap :center="{lat: 50.6272265, lng: 3.0571581}" :zoom="12" style="width: 100%; height: 600px" @click="onMapClick">
      <gmap-custom-marker
        key="supermarker"
        :marker="markerCenter">
        <img src="https://vuejs.org/images/logo.png" height="75"/>
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
      markerCenter: {
        latitude: 50.6272265,
        longitude: 3.0571581
      }
    }
  },
  methods: {
    onMapClick (event) {
      console.log('on map click', event)
      console.log('click coords', {lat: event.latLng.lat(), lng: event.latLng.lng()})
      this.markerCenter = {
        latitude: event.latLng.lat(),
        longitude: event.latLng.lng()
      }
    }
  }
}
</script>

<style>
</style>
