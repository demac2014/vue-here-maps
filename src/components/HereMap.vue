<template>
  <div>
    <input type="text" v-model="query" />
    <button type="button" v-on:click="search()">Search</button>
    <div class="here-map">
      <div ref="map" v-bind:style="{ width: width, height: height }"></div>
    </div>
  </div>
</template>

<script>
//import axios from 'axios';

export default {
  name: "HereMap",
  data() {
    return {
      map: {},
      platform: {},
      ui: {},
      behaviour: {},
      query: "Subway"
    }
  },
  props: {
    apikey: String,
    lat: String,
    lng: String,
    width: String,
    height: String
  },
  created() {
    this.platform = new window.H.service.Platform({
        apikey: this.apikey
    });
    //this.search = new window.H.places.Search(this.platform.getPlacesService());
  },
  mounted() {
    var defaultLayers = this.platform.createDefaultLayers();
    this.map = new window.H.Map(
      this.$refs.map,
      defaultLayers.vector.normal.map,
      {
          zoom: 15,
          center: { lng: this.lng, lat: this.lat }
      }
    );
    // eslint-disable-next-line no-unused-vars
    this.behavior = new window.H.mapevents.Behavior(new window.H.mapevents.MapEvents(this.map));
    // eslint-disable-next-line no-unused-vars
    this.ui = window.H.ui.UI.createDefault(this.map, defaultLayers);
    
    this.map.addEventListener('tap', evt => {
      var coord = this.map.screenToGeo(evt.currentPointer.viewportX, evt.currentPointer.viewportY);
      this.lat = coord.lat;
      this.lng = coord.lng;
      // console.log('Clicked at ' + Math.abs(coord.lat.toFixed(4)) +
      //     ((coord.lat > 0) ? 'N' : 'S') +
      //     ' ' + Math.abs(coord.lng.toFixed(4)) +
      //      ((coord.lng > 0) ? 'E' : 'W'));
    });
  },
  methods: {
    search() {
      
      this.map.removeObjects(this.map.getObjects());
      //this.search.request({ "q": query, "at": this.lat + "," + this.lng }, {}, data => {
      //var rest_api = "https://places.ls.hereapi.com/places/v1/discover/search?apiKey=" + this.apikey + "&at="+ this.lat + "," + this.lng + "&q=" + this.query;
      //console.log("called - " + rest_api);
      // axios
      //   .get(rest_api)
      //   .then(response => {
      //     console.log("Im in results");
      //     var data = response.data;
      //     for(var i = 0; i < data.results.items.length; i++) {
      //       this.dropMarker({ "lat": data.results.items[i].position[0], "lng": data.results.items[i].position[1] }, data.results.items[i]);
      //     }
      //   })
      //   .catch(error => {
      //     console.error(error);
      //   })
      var places = this.platform.getPlacesService();
      places.search({
        'at': this.lat + "," + this.lng,
        'q': this.query
      }, data => {
          for(var i = 0; i < data.results.items.length; i++) {
            this.dropMarker({ "lat": data.results.items[i].position[0], "lng": data.results.items[i].position[1] }, data.results.items[i]);
          }
      }, error => {
        console.error(error);
      });
    },
    dropMarker(coordinates, data) {
      var marker = new window.H.map.Marker(coordinates);
      marker.setData("<p>" + data.title + "<br>" + data.vicinity + "</p>");
      marker.addEventListener('tap', event => {
        var bubble = new window.H.ui.InfoBubble(event.target.getGeometry(), {
          content: event.target.getData()
        });
        this.ui.addBubble(bubble);
      }, false);
      this.map.addObject(marker);
    }
  }
}
</script>

<style scoped>

</style>
