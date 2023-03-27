<template>
  <div ref="map-root"></div>
</template>

<script>
import GeoJSON from 'ol/format/GeoJSON';
import View from 'ol/View'
import Map from 'ol/Map'
import VectorSource from 'ol/source/Vector';
import {Tile as TileLayer, Vector as VectorLayer} from 'ol/layer';
import OSM from 'ol/source/OSM'
import {bbox as bboxStrategy} from 'ol/loadingstrategy';
import 'ol/ol.css'

export default {
  name: 'App',
  mounted() {
    const vectorSource = new VectorSource({
      format: new GeoJSON(),
      url: function (extent) {
        return (
          'https://geohistoricaldata.org/geoserver/wfs?service=WFS&' +
          'version=1.1.0&request=GetFeature&typename=digikar:Orte&' +
          'outputFormat=application/json&srsname=EPSG:4326&' +
          'bbox=' +
          extent.join(',') +
          ',EPSG:4326'
        );
      },
      strategy: bboxStrategy,
    });

    const vector = new VectorLayer({
      source: vectorSource,
    });

    new Map({
      target: this.$refs['map-root'],
      layers: [
        new TileLayer({
          source: new OSM()
        }),
        vector
      ],
      view: new View({
        projection: 'EPSG:3857',
        center: [1500000, 6610000],
        zoom: 10,
        constrainResolution: true
      }),
    })
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

<style scoped>
 div {
  width: 100vw;
  height: 100vh;
 }
</style>
