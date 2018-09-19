<template>
  <div>
    <slot></slot>
  </div>
</template>
<script>
import Vue from 'vue';
import * as VueGoogleMaps from 'vue2-google-maps';
import {debounce} from 'underscore';
export default {
  mixins: [VueGoogleMaps.MapElementMixin],
  props: ['marker'],
  watch: {
    marker: function (val) {
     this.$mapPromise.then(map => this.$overlay.setPosition());
    },
  },
  provide: function () {
    this.$mapPromise.then(map => this.$overlay = this.initOverlay(map));
  },
  computed: {
    bounds () {
      const lat = parseFloat(this.marker.latitude)
      const lng = parseFloat(this.marker.longitude)
      return new google.maps.LatLngBounds(
        new google.maps.LatLng(lat, lng),
        new google.maps.LatLng(lat + 0.01, lng + 0.01)
      )
    },
    position () {
      var self = this;
      return {
        lat () {
          return parseFloat(self.marker.latitude);
        },
        lng () {
          return parseFloat(self.marker.longitude);
        }
      }
    }
  },
  destroyed: function() {
    this.$overlay.setMap(null);
    this.$overlay = undefined;
  },
  methods: {
    initOverlay (map) {

      var self = this;
      Overlay.prototype = new google.maps.OverlayView();

      /** @constructor */
      function Overlay(map) {
        this.setMap(map);
        const draw = () => {
          var posPixel = this.getProjection().fromLatLngToDivPixel(self.position);
          var x = Math.round(posPixel.x - (this._div.offsetWidth / 2));
          var y = Math.round(posPixel.y - this._div.offsetHeight - 10); // 10px for anchor
          this._div.style.left = x + "px";
          this._div.style.top = y + "px";
        };
        this.draw = draw
        this.setPosition = draw
      }

      /**
       * onAdd is called when the map's panes are ready and the overlay has been
       * added to the map.
       */
      Overlay.prototype.onAdd = function() {
        var div = self.$el;
        div.style.position = 'absolute';
        div.style.display = 'inline-block';
        div.style.zIndex = 1000;
        this._div = div;
        this.visible = true;

        var panes = this.getPanes();
        panes.overlayLayer.appendChild(div);
        panes.overlayMouseTarget.appendChild(div);
      };


      Overlay.prototype.onRemove = function() {
      	this._div = undefined;
      };

      return new Overlay(map);
    }
  }
};
</script>
