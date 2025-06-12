<template>
  <div id="map" class="map-container"></div>
</template>

<script setup>
import { onMounted, ref, watch} from 'vue';
import L from 'leaflet';

const polygonCoords = [
  [38.8977, -77.0365],
  [38.8979, -77.0362],
  [38.8981, -77.0364],
  [38.8977, -77.0365], // close the polygon
];

const props = defineProps({
  geojson: {
    type: Object,
    required: true,
  }
});

const selectedSegment = ref(null);
const geoJsonLayer = ref(null);
let segmentLayers = [];
let map;



onMounted(() => {
map = L.map('map').setView(polygonCoords[0], 17);

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors'
  }).addTo(map);

  // Draw full polygon
//   L.polygon(polygonCoords, {
//     color: 'blue',
//     weight: 1,
//     fillOpacity: 0.1
//   }).addTo(map);

  // Add individual line segments
//   for (let i = 0; i < polygonCoords.length - 1; i++) {
//     const segment = [polygonCoords[i], polygonCoords[i + 1]];
//     const line = L.polyline(segment, {
//       color: 'gray',
//       weight: 3
//     }).addTo(map);

//     line.on('click', () => {
//       highlightSegment(line);
//       selectedSegment.value = segment;
//       console.log('Selected Segment:', segment);
//     });

//     segmentLayers.push(line);
//   }
});



watch(() => props.geojson, (newGeojson) => {
    if (!map) return;
    if (geoJsonLayer.value) {
        geoJsonLayer.value.remove();  // Remove old layer
    }
    addGeoJsonLayer(newGeojson);

    const setbackFeature = newGeojson.features.find(feature => 
    feature.properties && feature.properties.poly_type === "setback"
    );

    if (setbackFeature) {
        let setbackCoords = setbackFeature.geometry.coordinates.map(ring => 
            ring.map(([lon, lat]) => [lat, lon]));
        setbackCoords[0].push(setbackCoords[0][0])
        //  add line segments
        for (let i = 0; i < setbackCoords[0].length - 1; i++) {
            const segment = [setbackCoords[0][i], setbackCoords[0][i + 1]];
            const line = L.polyline(segment, {
                color: 'gray',
                weight: 3
            }).addTo(map);

            line.on('click', () => {
                highlightSegment(line);
                selectedSegment.value = segment;
                console.log('Selected Segment:', segment);
            });
            segmentLayers.push(line);
        }

    }else{
        console.log("no setback coords")
    }

}, { immediate: true });

function addGeoJsonLayer(data) {
  geoJsonLayer.value = L.geoJSON(data).addTo(map);
  // Optional: zoom to layer bounds
  if (geoJsonLayer.value.getBounds) {
    map.fitBounds(geoJsonLayer.value.getBounds());
  }
}

function highlightSegment(selectedLine) {
  segmentLayers.forEach(line => {
    line.setStyle({ color: 'gray', weight: 3 });
  });
  selectedLine.setStyle({ color: 'red', weight: 5 });
}
</script>

<style scoped>
    .map-container {
        height: 100vh;
        width: 100%;
        border: 1px solid #ccc;
        border-radius: 8px;
    }
</style>
