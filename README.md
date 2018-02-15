Custom marker component for vue 2 js google map
-----------------------------------------------

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
  :onClick="placeClick"
  class="">
  >
      <img src="http://lorempixel.com/800/600/nature/" />
      <my-component :place="marker"></my-component>
</gmap-custom-marker>
```

At the moment, the component api looks like :
  
  * handles onClick callback and give as first argument the given prop `:marker`

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
 
 * refresh management using vue key change (this is a kind of hack)
```
 :key="marker.id + (marker.condition ? '-refresh-tag' : 0)"
```

Licence
-------

MIT
https://fr.wikipedia.org/wiki/Licence_MIT

