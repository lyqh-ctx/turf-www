<template>
  <div id='turfMap' style="height: 325px"></div>
</template>

<script>
// require('leaflet-fullscreen')

var turfMap = null //eslint-disable-line
var fg = null //eslint-disable-line
var control = null //eslint-disable-line
var layerArray = null //eslint-disable-line

export default {
  name: 'Map',
  mounted: function () {
    L.mapbox.accessToken = 'pk.eyJ1IjoidHVyZmpzIiwiYSI6ImNrZWp2ODRvNzFqMHoyeHJ6b3Jpc29iczQifQ.YdYb6a6rA5aCtkmDZ5wn_g'
    turfMap = L.mapbox.map('turfMap').addLayer(L.mapbox.styleLayer('mapbox://styles/mapbox/streets-v11'))
    control = L.control.layers([], []).addTo(turfMap)
    // turfMap.addControl(new L.Control.Fullscreen())
    fg = L.featureGroup([]).addTo(turfMap)
  },
  methods: {
    moveMapToExample (module) {
      if (!module.hasMap) return
      fg.eachLayer(function (layer) {
        control.removeLayer(layer)
      })
      fg.clearLayers()
      layerArray = module.example.split(/\n\/\/addToMap/)[1]
      layerArray = layerArray.match(/\[.*]/)[0]
      layerArray = layerArray.replace('[', '').replace(']', '').split(',')
      var zoomText = this.getLayersAndZoom()
      return eval(module.example + zoomText) //eslint-disable-line
    },
    getLayersAndZoom: function (layerArray) {
      return `
      addToMap.forEach(function (geojson, index) {
        var geojsonLayer = L.mapbox.featureLayer(geojson);
        geojsonLayer.eachLayer(function (layer) {
          if (layer.feature.properties){
            if (Object.keys(layer.feature.properties).length > 0){
              layer.bindPopup(JSON.stringify(layer.feature.properties));
            }
          }
        });
        control.addOverlay(geojsonLayer, layerArray[index])
        fg.addLayer(geojsonLayer)
      })
      turfMap.fitBounds(fg.getBounds(), {padding: [30,30]})`
    }
  }
}
</script>

<style lang="scss">
  #turfMap {
    margin-top: 30px;
  }
  .leaflet-touch .leaflet-control-fullscreen a {
  background-position: 0px 0px;
}
.leaflet-touch.leaflet-fullscreen-on .leaflet-control-fullscreen a {
  background-position: 0px -26px;
}
</style>
