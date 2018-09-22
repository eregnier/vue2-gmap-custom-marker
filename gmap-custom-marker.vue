<template><div><slot/></div></template>
<script>
import * as VueGoogleMaps from 'vue2-google-maps'
export default {
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
            const posPixel = projection.fromLatLngToDivPixel(self.position)
            self.$el.style.left = (posPixel.x + self.offsetX) + 'px'
            self.$el.style.top = (posPixel.y + self.offsetY) + 'px'
          }
        }
        onAdd () {
          const div = self.$el
          const panes = this.getPanes()
          div.style.position = 'absolute'
          div.style.display = 'inline-block'
          div.style.zIndex = 50
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
