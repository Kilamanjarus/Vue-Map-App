<script>
import mapboxgl from 'mapbox-gl'; // or "const mapboxgl = require('mapbox-gl');"
export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
    };
  },
  mounted: function () {
    this.createMap();
  },
  methods: {
    createMap: function () {
      console.log('Making the map...')

      mapboxgl.accessToken = process.env.VUE_APP_BRIAN_KEY;
      const coordinates = document.getElementById('coordinates');
      const map = new mapboxgl.Map({
        container: 'map', // container ID
        style: 'mapbox://styles/mapbox/satellite-streets-v11', // style URL
        center: [-88, 43], // starting position [lng, lat]
        zoom: 9, // starting zoom
        projection: 'globe' // display the map as a 3D globe
      });

      const layerList = document.getElementById('menu');
      const inputs = layerList.getElementsByTagName('input');

      for (const input of inputs) {
        input.onclick = (layer) => {
          const layerId = layer.target.id;
          map.setStyle('mapbox://styles/mapbox/' + layerId);
        };
      }

      map.on('style.load', () => {
        map.setFog({}); // Set the default atmosphere style
      });


      const marker = new mapboxgl.Marker({
        draggable: true
      })
        .setLngLat([-88, 42])
        .addTo(map);

      function onDragEnd() {
        const lngLat = marker.getLngLat();
        coordinates.style.display = 'block';
        coordinates.innerHTML = `Longitude: ${lngLat.lng}<br />Latitude: ${lngLat.lat}`;
      }

      marker.on('dragend', onDragEnd);

      map.on('load', () => {
        map.addSource('dem', {
          'type': 'raster-dem',
          'url': 'mapbox://mapbox.mapbox-terrain-dem-v1'
        });
        map.addLayer(
          {
            'id': 'hillshading',
            'source': 'dem',
            'type': 'hillshade'
            // insert below waterway-river-canal-shadow;
            // where hillshading sits in the Mapbox Outdoors style
          },
          'waterway-river-canal-shadow'
        );
      });

    }
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
  </div>
  <button @click="createMap()">Make Map</button>

  <div id='map' style='width: 800px; height: 800px;'></div>
  <pre id="coordinates" class="coordinates"></pre>
  <div id="menu">
    <input id="satellite-streets-v11" type="radio" name="rtoggle" value="3d" checked="checked">
    <label for="satellite-streets-v11">Satellite Enhanced</label>
    <input id="satellite-v9" type="radio" name="rtoggle" value="satellite">
    <!-- See a list of Mapbox-hosted public styles at -->
    <!-- https://docs.mapbox.com/api/maps/styles/#mapbox-styles -->
    <label for="satellite-v9">satellite</label>
    <input id="light-v10" type="radio" name="rtoggle" value="light">
    <label for="light-v10">light</label>
    <input id="dark-v10" type="radio" name="rtoggle" value="dark">
    <label for="dark-v10">dark</label>
    <input id="streets-v11" type="radio" name="rtoggle" value="streets">
    <label for="streets-v11">streets</label>
    <input id="outdoors-v11" type="radio" name="rtoggle" value="outdoors">
    <label for="outdoors-v11">outdoors</label>

  </div>
</template>

<style>
body {
  margin: 0;
  padding: 0;
}

#map {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}

.coordinates {
  background: rgba(0, 0, 0, 0.5);
  color: #fff;
  position: absolute;
  bottom: 40px;
  left: 10px;
  padding: 5px 10px;
  margin: 0;
  font-size: 11px;
  line-height: 18px;
  border-radius: 3px;
  display: none;
}

#menu {
  position: absolute;
  background: #efefef;
  padding: 10px;
  font-family: 'Open Sans', sans-serif;
}
</style>