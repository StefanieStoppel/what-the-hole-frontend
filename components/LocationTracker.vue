<template>
  <v-container fluid>
    <h2 class="text-center">Location Tracking</h2>
    <v-row>
      <v-col cols="6">
        <v-btn v-if="locationAvailable && !isWatchingLocation" @click="startWatchingPosition">Start</v-btn>
        <v-btn v-else-if="locationAvailable && isWatchingLocation" @click="stopWatchingPosition">Stop</v-btn>
      </v-col>
      <v-col cols="6">
        <v-btn v-if="!!xmlBlobUrl"
               :href="xmlBlobUrl"
               :download="'test.gpx'"
        >Download GPX</v-btn>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        Current location: {{this.currentGpxCoordinate.latitude}}, {{this.currentGpxCoordinate.longitude}},
        Timestamp: {{this.currentGpxCoordinate.time}}
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import createGpx from 'gps-to-gpx';

class GPXCoordinate {
  constructor(latitude, longitude, time) {
    this.latitude = latitude;
    this.longitude = longitude;
    this.time = time;
  }
}

export default {
  name: "LocationTracker",
  data() {
    return {
      locationAvailable: false,
      isWatchingLocation: false,
      infoText: '',
      locationWatcherOptions: {
        enableHighAccuracy: true,
        timeout: 5000,
        maximumAge: 0
      },
      locationWatcherId: null,
      currentGpxCoordinate: new GPXCoordinate(null, null, null),
      gpxData: {
        startTime: null,
        endTime: null,
        waypoints: []
      },
      xmlBlobUrl: null
    }
  },
  mounted() {
    if (!navigator.geolocation) {
      this.locationAvailable = false;
      this.infoText = 'Geolocation is not supported by your browser';
    } else {
      this.locationAvailable = true;
    }
  },
  methods: {
    getCurrentISOTimestamp() {
      return new Date().toISOString();
    },
    startWatchingPosition() {
      this.infoText = 'Locating…';
      this.isWatchingLocation = true;
      this.locationWatcherId = navigator.geolocation.watchPosition(this.success, this.error);
      this.gpxData.startTime = this.getCurrentISOTimestamp();
      console.log("Started watcher")
    },
    stopWatchingPosition() {
      this.infoText = 'Done';
      this.isWatchingLocation = false;
      navigator.geolocation.clearWatch(this.locationWatcherId);
      console.log("Stopped watcher")
      this.gpxData.endTime = this.getCurrentISOTimestamp();
      this.createGpxFile();
    },
    success(position) {
      this.addTimestampedGPXCoordinateToWaypoints(position);
    },
    error(error) {
      console.warn('ERROR(' + error.code + '): ' + error.message);
    },
    addTimestampedGPXCoordinateToWaypoints(position) {
      const timestampISO = new Date(position.timestamp).toISOString();
      const coords = position.coords;
      const gpxCoordinate =
        new GPXCoordinate(coords.latitude, coords.longitude, timestampISO);
      this.gpxData.waypoints.push(gpxCoordinate);
      this.updateCurrentGPXCoordinate(gpxCoordinate)
      console.log('Adding new coordinate to waypoints...')
    },
    updateCurrentGPXCoordinate(gpxCoordinate) {
      this.currentGpxCoordinate = gpxCoordinate
    },
    createGpxFile() {
      console.log(this.gpxData)
      const gpx = createGpx(this.gpxData.waypoints, {startTime: this.gpxData.startTime});
      // console.log(gpx)
      const parser = new DOMParser();
      const xmlDoc = parser.parseFromString(gpx, "text/xml");
      const xmlBlob = new Blob([gpx], {type: 'text/xml'});
      this.xmlBlobUrl = URL.createObjectURL(xmlBlob);
    }
  }
}
</script>

<style scoped>

</style>
