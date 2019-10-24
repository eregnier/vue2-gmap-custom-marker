<template>
    <div id="app">
        <div class="card-tools" v-if="tools">
          <center>
          <button
              class="button add-marker"
              :style="{'background-color': addMode ? '#66f13d' : '#3d66f1'}"
              @click="addMode=true"
          >{{ addMode ? 'Click on the map' : '🞧 Add marker' }}</button>
          <br/>
          <br/>
          <button
              class="button weather add-marker"
              :style="{'background-color': addWeather ? '#6f163d' : '#6d63f1'}"
              @click="addWeather=!addWeather"
          >{{ addWeather ? 'Weather marker' : '★ Icon Marker' }}</button>
          </center>
          <hr/>
          <div class="batch-cluster">
              <label>Add</label>
              <input type="number" min="0" max="100" v-model="batchCount" />
              <label>random markers</label>
              <button @click="addbatch">🞧</button>
          </div>
          <hr/>
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
          <hr/>
          <div class="animate-panel">
              <center>
                  <label>
                      <a href="https://daneden.github.io/animate.css/">Animate.css</a> type
                  </label>
                  <br />
                  <br />
                  <select v-model="selectedAnimation">
                      <option v-for="animation in animations" :key="animation">{{animation}}</option>
                  </select>
                  <br />
                  <br />
                  <label>infinite animation</label>
                  <button @click="infinite=!infinite">
                      <span v-if="infinite">ON</span>
                      <span v-else>OFF</span>
                  </button>
              </center>
          </div>
          <hr/>
          <div class="live-editor">
              <textarea v-model="html"></textarea>
          </div>
          <hr/>
          <center>
            <button class="hide-tool" @click="displayTools(false)">hide tools</button>
          </center>
        </div>
        <button v-else class="show-tool" @click="displayTools(true)">show tools</button>

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
                    <weather v-if="marker.weather" :coords="marker" />
                    <img
                        :class="animation"
                        v-else
                        :title="JSON.stringify(marker)"
                        class="icon-sm"
                        :src="src"
                        height="45"
                    />
                </gmap-custom-marker>
            </cluster>
            <cluster>
                <gmap-custom-marker v-for="(marker, i) in batchMarkers" :key="i" :marker="marker">
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        xmlns:xlink="http://www.w3.org/1999/xlink"
                        version="1.1"
                        id="Layer_1"
                        x="0px"
                        y="0px"
                        viewBox="0 0 376.277 376.277"
                        style="enable-background:new 0 0 376.277 376.277;"
                        xml:space="preserve"
                        width="50"
                        height="50"
                    >
                        <g>
                            <path
                                style="fill:#B27214;"
                                d="M339.114,274.035c26.58-2.67,40.36,30.68,19.52,47.38c-4.7,3.77-9.56,7.34-14.57,10.71   L339.114,274.035z"
                            />
                            <path
                                style="fill:#B27214;"
                                d="M54.864,17.645c16.7-20.84,50.05-7.06,47.38,19.51c-0.73,7.19-1.1,14.48-1.1,21.86l-61.21-20.26   C44.494,31.415,49.484,24.365,54.864,17.645z"
                            />
                            <path
                                style="fill:#F7B239;"
                                d="M339.114,274.035l4.95,58.09c-34.42,23.15-75.86,36.65-120.45,36.65   c-119.69,0-216.11-96.42-216.11-216.11c0-41.81,11.87-80.84,32.43-113.91l61.21,20.26c0,119.27,96.84,216.12,216.12,216.11   C324.644,275.125,331.934,274.755,339.114,274.035z"
                            />
                            <path
                                style="fill:#E09B2D;"
                                d="M54.002,152.666c0-36.481,9.039-70.844,25-100.979L39.934,38.756   c-20.56,33.07-32.43,72.1-32.43,113.91c0,119.69,96.42,216.11,216.11,216.11c7.843,0,15.586-0.426,23.214-1.241   C138.162,355.984,54.002,264.486,54.002,152.666z"
                            />
                            <path
                                style="fill:#FFFFFF;"
                                d="M250.505,296.224c-0.69,0-1.393-0.096-2.089-0.297c-39.542-11.444-76.026-33.077-105.509-62.56   c-33.14-33.14-56.039-74.548-66.222-119.749c-0.91-4.041,1.628-8.055,5.668-8.965c4.042-0.909,8.055,1.627,8.965,5.668   c9.559,42.428,31.065,81.309,62.195,112.439c27.697,27.697,61.956,48.015,99.072,58.757c3.979,1.151,6.271,5.31,5.119,9.289   C256.756,294.089,253.758,296.224,250.505,296.224z"
                            />
                            <circle style="fill:#FFFFFF;" cx="78.567" cy="79.428" r="7.5" />
                            <g>
                                <path
                                    style="fill:#333333;"
                                    d="M373.635,287.714c-5.884-14.403-19.741-22.705-35.267-21.138c-6.962,0.7-14.063,1.054-21.107,1.054    c-55.683,0-108.06-21.71-147.482-61.133c-39.423-39.423-61.133-91.8-61.132-147.483c0-7.04,0.355-14.14,1.055-21.105    c1.562-15.54-6.736-29.383-21.139-35.266C74.239-3.208,58.714,0.842,49.007,12.958C17.405,52.404,0,102.019,0,152.664    c0,59.889,23.215,116.087,65.37,158.242c42.154,42.155,98.352,65.371,158.241,65.371c50.646,0,100.262-17.404,139.708-49.007    C375.436,317.563,379.485,302.036,373.635,287.714z M60.713,22.337c4.288-5.352,9.65-7.301,14.631-7.301    c2.706,0,5.299,0.576,7.546,1.493c6.447,2.634,12.955,9.239,11.886,19.88c-0.409,4.069-0.697,8.182-0.885,12.303L51.535,34.694    C54.432,30.49,57.486,26.366,60.713,22.337z M75.977,300.299C36.655,260.978,15,208.547,15,152.664    c0-37.508,9.681-73.26,28.22-104.922l50.493,16.711c1.368,57.654,24.49,111.68,65.459,152.65    c42.256,42.256,98.399,65.527,158.089,65.526c5,0,10.024-0.183,15.018-0.514l3.932,46.219    c-33.51,21.614-72.027,32.942-112.599,32.942C167.729,361.277,115.298,339.621,75.977,300.299z M353.94,315.564    c-1.168,0.936-2.344,1.858-3.527,2.766l-3.085-36.257c6.339,1.738,10.469,6.537,12.42,11.313    C362.355,299.769,362.199,308.947,353.94,315.564z"
                                />
                                <path
                                    style="fill:#333333;"
                                    d="M283.778,317.604c-5.679,0.463-11.464,0.698-17.192,0.698c-55.882,0-108.313-21.656-147.635-60.978    c-39.321-39.322-60.976-91.753-60.976-147.636c0-8.267,0.488-16.59,1.451-24.74c0.486-4.113-2.455-7.842-6.568-8.328    c-4.111-0.483-7.842,2.455-8.328,6.568c-1.032,8.731-1.555,17.647-1.555,26.5c0,59.889,23.215,116.087,65.37,158.242    s98.353,65.371,158.241,65.371c6.135,0,12.329-0.251,18.413-0.748c4.128-0.337,7.201-3.957,6.865-8.085    C291.526,320.341,287.903,317.267,283.778,317.604z"
                                />
                            </g>
                        </g>
                    </svg>
                </gmap-custom-marker>
            </cluster>
            <gmap-custom-marker alignment="bottomright" key="supermarker" :marker="markerCenter">
                <div class="card" @click="e => e.stopPropagation()">
                    <center>
                        <h3>This is a marker</h3>
                        <p>Lat : {{markerCenter.lat}}, Lng : {{markerCenter.lng}}</p>
                    </center>
                    <img :class="animation" class="icon" :src="src" height="75" />
                    <div class="input-group">
                        <center>
                            <label>Set marker image urls</label>
                        </center>
                        <br />
                        <input type="input" v-model="testText" />
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
            batchCount: 10,
            html:
                '<center><h2>This is <u>live</u> html marker 🔥</h2></center> <iframe width="330" height="170" src="https://www.youtube.com/embed/RySHDUU2juM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>',
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
            animations: require("./animations.json"),
            batchMarkers: [],
            tools: true
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
      displayTools(value) {
        this.tools = value
      },
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
        },
        addbatch() {
            this.batchMarkers = [];
            for (let i = 0; i < this.batchCount; i++) {
                this.batchMarkers.push({
                    lat: `48.1${String(parseInt(Math.random() * 100000))}`,
                    lng: `-1.7${String(parseInt(Math.random() * 100000))}`
                });
            }
            this.markerCenter = {
                lat: 48.1030572,
                lng: -1.7065389
            };
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
  font-family: 'Kosugi Maru', sans-serif;
  color: #444;
}

button {
    color: #484b49;
    margin: 10px;
    border-radius: 5px;
    background-color: white;
    border: 1px solid #999;
    font-size: 1.05em;
    padding-right:10px;
    padding-left:10px;
    padding-top:5px;
    padding-bottom:5px;
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
    font-size: 1.3em;

}
.zindex-a {
    background-color: #eb7bff;
}
.zindex-b {
    background-color: #17e2b8;
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
    color: #28a745;
    width: 80px;
    padding: 5px;
    border-radius: 5px;
    background-color: white;
    border:1px solid #28a745;
    margin: 2px;

}

.alignment {
    right: 10px;
    z-index: 1000;
    padding: 4px;
    top: 15px;
    border-radius: 5px;
}
.button {
    left: 50%;
    top: 15px;
    z-index: 100;
    color: white;
    padding: 5px;
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
    right: 10px;
    top: 150px;
    height: 100px;
    width: 250px;
    z-index: 100;
}

.live-editor {
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

.batch-cluster {
    right: 0px;
    top: 400px;
    width: 270px;
    height: 40px;
    border-radius: 5px;
    z-index: 100;
}

.card-tools {
    right: 5px;
    top: 5px;
    padding: 8px;
    background-color: #fafafa;
    border: 1px solid #bbb;
    width: 270px;
    z-index: 100;
    position:absolute;
    box-shadow: rgba(0, 0, 0, 0.3) 0px 1px 4px -1px;
}

.batch-cluster > input,
.batch-cluster > button {
    margin-left: 10px;
    margin-right: 10px;
}
.hide-tool {
  background-color: #fafafa;
  border: 1px solid #bbb;
  font-size: 1.3em;

}

.show-tool {
  position: absolute;
  top: 5px;
  right: 5px;
  background-color: #fafafa;
  border: 1px solid #bbb;
  font-size: 1.3em;
  z-index: 100;
}

</style>
