<template>
  <v-container fluid>
    <h2 class="text-center">Location Tracking</h2>
    <v-row v-if="locationAvailable">
      <v-col cols="12">
        Current location: {{this.currentGpxCoordinate.latitude}}, {{this.currentGpxCoordinate.longitude}},
        Timestamp: {{this.currentGpxCoordinate.time}}
      </v-col>
    </v-row>
    <v-row v-else>
      <v-col cols="12">
        {{infoText}}
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
    start() {
      if (this.locationAvailable) {
        this.isWatchingLocation = true;
        this.locationWatcherId = navigator.geolocation.watchPosition(this.success, this.error);
        this.gpxData.startTime = this.getCurrentISOTimestamp();
        console.log("Started watcher")
      } else {
        throw new Error("Location not available")
      }
    },
    stop() {
      if (this.locationAvailable) {
        this.isWatchingLocation = false;
        navigator.geolocation.clearWatch(this.locationWatcherId);
        console.log("Stopped watcher")
        this.gpxData.endTime = this.getCurrentISOTimestamp();
        this.$emit('location-result', this.createGpxFile())
      } else {
        throw new Error("Location not available")
      }
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
      this.currentGpxCoordinate = gpxCoordinate
      console.log('Adding new coordinate to waypoints...')
    },
    createGpxFile() {
      console.log(this.gpxData)
      const gpx = createGpx(this.gpxData.waypoints, {startTime: this.gpxData.startTime});
      return new Blob([gpx], {type: 'text/xml'});
    }
  }
}
</script>

<style scoped>

</style>
