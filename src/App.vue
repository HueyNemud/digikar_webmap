<template>
  <div ref="map-root"></div>
</template>

<script>
import GeoJSON from 'ol/format/GeoJSON';
import View from 'ol/View'
import Map from 'ol/Map'
import VectorSource from 'ol/source/Vector';
import {Vector as VectorLayer} from 'ol/layer';
import OSM from 'ol/source/OSM';
import Colorize from 'ol-ext/filter/Colorize';
// import Stamen from 'ol/source/Stamen';
import {bbox as bboxStrategy} from 'ol/loadingstrategy';
import {
  Circle,
  Fill,
  Stroke,
  Style,
  Text,
} from 'ol/style';
import 'ol/ol.css'
import TileLayer from 'ol/layer/Tile';

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

    const placeStyle = function(feature) {
      return new Style({
          image: new Circle({
            radius: 5,
            fill: new Fill({
              color: '#b3e5f8'
            }),
            stroke: new Stroke({color: '#03a7e6', width: 1}),
          }),
          text: new Text({
            text: feature.get('ortname_hov'),
            font: "bold 13px lato ",
            stroke: new Stroke({
              color: "white",
              width: 4
            }),
            offsetX: 13,
            textAlign: 'start'
          })
        })
    }
    const vector = new VectorLayer({
      source: vectorSource,
      style: placeStyle,
      declutter: true
    });

    const osm = new TileLayer({
        source: new OSM()
      })
    osm.addFilter(new Colorize({ operation:'grayscale', value: 1.0 }))

    new Map({
      target: this.$refs['map-root'],
      layers: [
        osm,
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
}
</style>

<style scoped>
 div {
  width: 100vw;
  height: 100vh;
 }
</style>
