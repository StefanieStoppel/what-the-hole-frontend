<template>
  <v-container fluid>
    <h2 class="text-center">Location Tracking</h2>
    <v-row>
      <v-col cols="12">
        <v-btn v-if="locationAvailable && !isWatchingLocation" @click="startWatchingPosition">Start</v-btn>
        <v-btn v-else-if="locationAvailable && isWatchingLocation" @click="stopWatchingPosition">Stop</v-btn>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        Current location: {{this.currentLatitude}}, {{this.currentLongitude}}
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
class TimestampedCoordinate {
  constructor(latitude, longitude, timestamp) {
    this.latitude = latitude;
    this.longitude = longitude;
    this.timestamp = timestamp;
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
      timestampedCoordinateHistory: [],
      currentLatitude: null,
      currentLongitude: null
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
    startWatchingPosition() {
      this.infoText = 'Locating…';
      this.isWatchingLocation = true;
      this.locationWatcherId = navigator.geolocation.watchPosition(this.success, this.error);
      console.log("Started watcher")
    },
    stopWatchingPosition() {
      this.infoText = 'Done';
      this.isWatchingLocation = false;
      navigator.geolocation.clearWatch(this.locationWatcherId);
      console.log("Stopped watcher")
    },
    success(position) {
      const coords = position.coords;
      this.currentLatitude = coords.latitude;
      this.currentLongitude = coords.longitude;
      const timestampedCoordinate = new TimestampedCoordinate(coords.latitude, coords.longitude, Date.now())
      this.timestampedCoordinateHistory.push(timestampedCoordinate);
      console.log("Current location: " + coords.latitude + ", " + coords.longitude)
      console.log("Location history: " + this.timestampedCoordinateHistory)
    },
    error(error) {
      console.warn('ERROR(' + error.code + '): ' + error.message);
    }
  }
}
</script>

<style scoped>

</style>
