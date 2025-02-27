<template>
  <div>
    <VueDatePicker v-model="date"></VueDatePicker>
    <p>Selected Date: {{ date }}</p>
    <div id="mapid" style="height:512px; width:545px"></div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import VueDatePicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css'
import 'leaflet/dist/leaflet.css';
import L from 'leaflet'; // Import Leaflet in the <script setup> block

const date = ref();
const dopData = ref([]);

// Fetch Dop data from FastAPI
const fetchData = async () => {
  try {
    const response = await fetch('http://localhost:8000/dop-data');  // Your FastAPI endpoint
    dopData.value = await response.json();
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};

// Initialize map after fetching data
onMounted(async () => {
  await fetchData(); // Fetch data from FastAPI
  initializeMap();   // Initialize map after data is fetched
});

// Initialize the map and add markers
const initializeMap = () => {
  // Check if map is already initialized
  if (window.map) {
    return; // If the map already exists, do not initialize it again
  }

  window.map = L.map('mapid').setView([0, 0], 2); // Adjust zoom level as needed
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(window.map);

  dopData.value.forEach((point) => {
    L.marker([point.latitude, point.longitude]).addTo(window.map)
      .bindPopup(`PDOP: ${point.pdop}`);
  });
};
</script>

<script>
export default {
  data() {
    return {
      data: {
        x: [],
        y: [],
        z: []
      },
      latMin: -91.44,
      latMax: 91.44,
      lngMin: -181.44,
      lngMax: 181.44,
      interval: 0.72,
      colors: [
        { color: "#00008f", point: 0 },
        { color: "#0000ef", point: 0.11111111111 },
        { color: "#005fff", point: 0.22222222222 },
        { color: "#00cfff", point: 0.33333333333 },
        { color: "#4fffaf", point: 0.44444444444 },
        { color: "#bfff3f", point: 0.55555555556 },
        { color: "#ffcf00", point: 0.66666666667 },
        { color: "#ff5f00", point: 0.77777777778 },
        { color: "#ef0000", point: 0.88888888889 },
        { color: "#ef0000", point: 0.99999999999 },
        { color: "#7f0000", point: 1.0 },
      ]
    };
  },
  methods: {
    async parseTextFile(filePath) {
      const response = await fetch(filePath);
      const text = await response.text();
      const rows = text.split("\n").filter((line) => line.trim() !== "");
      const parsedData = [];
      rows.forEach((row) => {
        const [lat, lng, gdop, pdop, hdop, vdop, tdop, numInView] = row.split(/\s+/);
        parsedData.push({
          latitude: parseFloat(lat),
          longitude: parseFloat(lng),
          gdop: parseFloat(gdop),
          pdop: parseFloat(pdop),
          hdop: parseFloat(hdop),
          vdop: parseFloat(vdop),
          tdop: parseFloat(tdop),
          numInView: parseInt(numInView)
        });
      });
      return parsedData;
    },
    getIndices(lat, lng, latMax, lngMin, interval) {
      const i = Math.round((latMax - lat) / interval);
      const j = Math.round((lng - lngMin) / interval);
      return { i, j };
    },
    generateData() {
      const numRows = Math.floor((this.latMax - this.latMin) / this.interval) + 1;
      const numCols = Math.floor((this.lngMax - this.lngMin) / this.interval) + 1;

      for (let i = 0; i < numRows; i++) {
        const rowX = [];
        const rowY = [];
        const rowZ = [];

        const lat = this.latMax - i * this.interval;

        for (let j = 0; j < numCols; j++) {
          const lng = this.lngMin + j * this.interval;

          rowX.push(lng);
          rowY.push(lat);
          rowZ.push(null);
        }

        this.data.x.push(rowX);
        this.data.y.push(rowY);
        this.data.z.push(rowZ);
      }

      console.log(this.data);
    },
    async updateZValues(filePath) {
      const parsedData = await this.parseTextFile(filePath);
      parsedData.forEach(({ latitude, longitude, pdop }) => {
        const { i, j } = this.getIndices(latitude, longitude, this.latMax, this.lngMin, this.interval);
        if (i >= 0 && i < this.data.z.length && j >= 0 && j < this.data.z[i].length) {
          this.data.z[i][j] = Math.floor(pdop);
        }
      });
    },
    getColor(value, min, max, colors) {
      function hex(c) {
        var s = "0123456789abcdef";
        var i = parseInt(c, 10);
        if (i === 0 || isNaN(c)) return "00";
        i = Math.round(Math.min(Math.max(0, i), 255));
        return s.charAt((i - (i % 16)) / 16) + s.charAt(i % 16);
      }
      function trim(s) {
        return s.charAt(0) === "#" ? s.substring(1, 7) : s;
      }
      function convertToRGB(hex) {
        var color = [];
        color[0] = parseInt(trim(hex).substring(0, 2), 16);
        color[1] = parseInt(trim(hex).substring(2, 4), 16);
        color[2] = parseInt(trim(hex).substring(4, 6), 16);
        return color;
      }
      function convertToHex(rgb) {
        return hex(rgb[0]) + hex(rgb[1]) + hex(rgb[2]);
      }

      if (value === null || isNaN(value)) {
        return "#ffffff";
      }
      if (value > max) {
        return colors[colors.length - 1].color;
      }
      if (value < min) {
        return colors[0].color;
      }
      var loc = (value - min) / (max - min);
      if (loc < 0 || loc > 1) {
        return "#fff";
      } else {
        var index = 0;
        for (var i = 0; i < colors.length - 1; i++) {
          if (loc >= colors[i].point && loc <= colors[i + 1].point) {
            index = i;
          }
        }
        var color1 = convertToRGB(colors[index].color);
        var color2 = convertToRGB(colors[index + 1].color);

        var f =
          (loc - colors[index].point) /
          (colors[index + 1].point - colors[index].point);
        var rgb = [
          color1[0] + (color2[0] - color1[0]) * f,
          color1[1] + (color2[1] - color1[1]) * f,
          color1[2] + (color2[2] - color1[2]) * f,
        ];

        return `#${convertToHex(rgb)}`;
      }
    },
  },
};
</script>

<style>
#mapid {
  background-color: black;
}
</style>
