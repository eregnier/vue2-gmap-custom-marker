Custom marker component for vue 2 js google map
-----------------------------------------------

Demo
----

[demo from gh-pages branch](https://eregnier.github.io/vue2-gmap-custom-marker/dist/)

`npm i vue2-gmap-custom-marker`

https://www.npmjs.com/package/vue2-gmap-custom-marker

This component let user display custom html content on the map using Overlay.
This component is an adaptation of the google map V3 overlay code sample with some great ideas from angularjs google map https://ngmap.github.io/ from this component https://github.com/allenhwkim/angularjs-google-maps/blob/master/directives/custom-marker.js

This project is a plugin for https://github.com/xkjyeah/vue-google-maps

Sample
------

![custom markers on vue google map](sample.png)

How to
------

Here is how to use this component

 * import the component and use it in your own vue map component



```
import GmapCustomMarker from 'vue2-gmap-custom-marker';

[...]

components: {
    'gmap-custom-marker': GmapCustomMarker
},
```


```
<gmap-custom-marker
  :key="marker.id + (selectedMarker.id === marker.id ? '-force-refresh' : 0)"
  v-for="marker in places"
  :marker="marker"
  @click.native="placeClick"
  :offset="10"
  class=""
  >
      <img src="http://lorempixel.com/800/600/nature/" />
      <my-component :place="marker"></my-component>
</gmap-custom-marker>
```

At the moment, the component api looks like :

  * handles click callback and give as first argument the given prop `:marker`

```
methods: {
   placeClick (marker) {
       console.log('this marker was clicked', marker)
   }
}
```

 * display places depending on their lng lat values

```
computed: {
   places () {
      return [
        {
          lat: 50.60229509638775,
          lng: 3.0247059387528408
        }
      ]
   }
}
```

 * manually specify a vertical offset value for the marker in pixels with prop `:offset`

 ```
 <gmap-custom-marker :offset="10"></gmap-custom-marker>
 ```

Licence
-------

MIT
https://en.wikipedia.org/wiki/MIT_License
