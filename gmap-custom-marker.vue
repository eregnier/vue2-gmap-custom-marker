<script>
import * as VueGoogleMaps from 'vue2-google-maps'
export default {
  template: '<div><slot/></div>',
  mixins: [VueGoogleMaps.MapElementMixin],
  props: {
    marker: {
      type: Object,
      default: undefined
    },
    offsetX: {
      type: Number,
      default: 0
    },
    offsetY: {
      type: Number,
      default: 0
    }
  },
  watch: {
    marker (val) {
     this.$mapPromise.then(map => this.$overlay.setPosition())
    },
  },
  provide () {
    const self = this
    this.$mapPromise.then(map => {
      class Overlay extends google.maps.OverlayView {
        constructor (map) {
          super()
          this.setMap(map)
          this.draw = () => this.repaint()
          this.setPosition = () => this.repaint()
        }
        repaint () {
          const projection = this.getProjection()
          if (projection && self.$el) {
            var posPixel = projection.fromLatLngToDivPixel(self.position)
            var x = posPixel.x - (self.$el.offsetWidth / 2) + self.offsetX
            var y = posPixel.y - self.$el.offsetHeight + self.offsetY
            self.$el.style.left = x + "px"
            self.$el.style.top = y + "px"
          }
        }
        onAdd () {
          const div = self.$el
          div.style.position = 'absolute'
          div.style.display = 'inline-block'
          div.style.zIndex = 1000
          this.visible = true

          var panes = this.getPanes()
          panes.overlayLayer.appendChild(div)
          panes.overlayMouseTarget.appendChild(div)
        }
        onRemove () {
          self.$el.remove()
        }
      }
      this.$overlay = new Overlay(map)
    })
  },
  computed: {
    bounds () {
      return new google.maps.LatLngBounds(
        new google.maps.LatLng(this.lat, this.lng),
        new google.maps.LatLng(this.lat + 0.01, this.lng + 0.01)
      )
    },
    lat () {
      return parseFloat(this.marker.lat || this.marker.latitude)
    },
    lng () {
      return parseFloat(this.marker.lng || this.marker.longitude)
    },
    position () {
      const self = this
      return {
        lat () {
          return self.lat
        },
        lng () {
          return self.lng
        }
      }
    }
  },
  destroyed () {
    this.$overlay.setMap(null)
    this.$overlay = undefined
  }
}
</script>
