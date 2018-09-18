<template>
  <div>
    <slot></slot>
  </div>
</template>
<script>
import Vue from 'vue';
import * as VueGoogleMaps from 'vue2-google-maps';
import _ from 'underscore';
export default {
  mixins: [VueGoogleMaps.MapElementMixin],
  props: ['marker', 'onClick'],
  watch: {
    marker: function (val) {
     this.$mapPromise.then(map => this.$overlay.draw());
    },
  },
  provide: function () {
    var self = this;
    this.$mapPromise.then(function (map) {
      self.$overlay = self.initOverlay(map);
    });
  },
  data () {
    return {
      clickListener: {remove () {}},
      touchListener: {remove () {}},
      previousLat: undefined,
      previousLng: undefined
    }
  },
  computed: {
    bounds () {
      var lat = parseFloat(this.marker.latitude);
      var lng = parseFloat(this.marker.longitude);
      return new google.maps.LatLngBounds(
        new google.maps.LatLng(lat, lng),
        new google.maps.LatLng(lat + 0.01, lng + 0.01));
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
    if (self.$el) {
      while (self.$el.firstChild) {
        self.$el.removeChild(self.$el.firstChild);
      }
    }
    this.$overlay = undefined;
    this.clickListener.remove();
    this.touchListener.remove();
  },
  methods: {
    initOverlay (map) {

      var self = this;
      Overlay.prototype = new google.maps.OverlayView();

      /** @constructor */
      function Overlay(map) {
        // Initialize all properties.
        this.map_ = map;
        // Explicitly call setMap on this overlay.
        this.setMap(map);

        var overlay = this;
        this.dragendListener = google.maps.event.addListener(map, 'dragend', function () {
	         if (overlay._div !== undefined) {
	            overlay._div.style.visibility = "hidden";
	         }
        });

      }


      Overlay.prototype.setPosition = function(position) {
        position = position || this.position;
        var _this = this;
        if (this.getProjection() && typeof position.lng == 'function') {
          var setPosition = function() {
            if (!_this.getProjection()) { return; }
            var posPixel = _this.getProjection().fromLatLngToDivPixel(self.position);
            var x = Math.round(posPixel.x - (_this._div.offsetWidth/2));
            var y = Math.round(posPixel.y - _this._div.offsetHeight - 10); // 10px for anchor
            _this._div.style.left = x + "px";
            _this._div.style.top = y + "px";
            _this._div.style.visibility = "visible";
          };
          if (_this._div.offsetWidth && _this._div.offsetHeight) {
            setPosition();
          } else {
            //delayed left/top calculation when width/height are not set instantly
            setTimeout(setPosition, 300);
          }
        }
      };

      /**
       * onAdd is called when the map's panes are ready and the overlay has been
       * added to the map.
       */
      Overlay.prototype.onAdd = function() {
        var div = self.$el;
        div.style.borderStyle = 'none';
        div.style.borderWidth = '0px';
        div.style.position = 'absolute';
        div.style.display = 'inline-block';
        div.style.zIndex = 1000;
        this._div = div;
        this.visible = true;

        var panes = this.getPanes();
        panes.overlayLayer.appendChild(div);
        panes.overlayMouseTarget.appendChild(div);

        var onClick = function() {
          if (self.onClick !== undefined) {
            self.onClick(self.marker);
          }
        };

        self.touchListener = google.maps.event.addDomListener(div, 'touchstart', onClick);
        self.clickListener = google.maps.event.addDomListener(div, 'mousedown', onClick);

      };

      Overlay.prototype.draw = _.debounce(function(e) {
        if(!this._div) {
          return;
        }
        // Retrieve the south-west and north-east coordinates of this overlay
        // in LatLngs and convert them to pixel coordinates.
        // We'll use these coordinates to resize the div.
        this.setPosition(self.position);

      }, 10);

      // The onRemove() method will be called automatically from the API if
      // we ever set the overlay's map property to 'null'.
      Overlay.prototype.onRemove = function() {
        this.map_ = undefined;
	      if (this._div !== undefined) {
	        this._div.remove();
        	this._div = undefined;
	      }
        this.dragendListener.remove();
      };

      return new Overlay(map);
    }
  }
};
</script>
