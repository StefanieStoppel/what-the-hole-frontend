<template>
  <v-container>
    <v-row justify="center" align="center">
      <v-col sm="6">
        <v-btn v-if="!running" @click="start">Start recording</v-btn>
        <v-btn v-else @click="stop">Stop recording</v-btn>
      </v-col>
      <v-col sm="6">
        <v-btn v-if="!!zipUrl"
               :href="zipUrl"
               :download="zipFileName"
        >Download ZIP
        </v-btn>
      </v-col>
    </v-row>
    <v-row>
      <v-col sm="12">
        <video-capture ref="video" @video-result="result => updateResults('videoResult' , result)"/>
        <location-tracker ref="location" @location-result="result => updateResults('locationResult' , result)"/>
        <accelerometer-display ref="accelerometer"
                               @accelerometer-result="result => updateResults('accelerometerResult' , result)"/>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'
import AccelerometerDisplay from '~/components/AccelerometerDisplay'
import LocationTracker from "~/components/LocationTracker";
import VideoCapture from "~/components/VideoCapture";
import JSZip from 'jszip';

export default {
  components: {
    LocationTracker,
    VideoCapture,
    Logo,
    VuetifyLogo,
    AccelerometerDisplay
  },
  data() {
    return {
      startTime: null,
      endTime: null,
      running: false,
      results: {
        videoResult: null,
        locationResult: null,
        accelerometerResult: null
      },
      zipUrl: null
    }
  },
  computed: {
    allResultsReceived() {
      return Object.values(this.results).every(result => result != null)
    },
    zipFileName() {
      return this.startTime ? `measurements-${this.startTime}.zip` : null
    }
  },
  watch: {
    allResultsReceived: 'generateZipUrl'
  },
  methods: {
    start() {
      this.running = true
      this.startTime = new Date().toISOString();
      this.$refs.video.start()
      this.$refs.location.start()
      this.$refs.accelerometer.start()
    },
    stop() {
      this.running = false
      this.endTime = new Date().toISOString();
      this.$refs.video.stop()
      this.$refs.location.stop()
      this.$refs.accelerometer.stop()
    },
    updateResults(key, value) {
      this.results[key] = value
    },
    generateZipUrl() {
      debugger
      const zip = new JSZip();
      zip.file(`video-${this.startTime}.webm`, this.results.videoResult)
        .file(`location-${this.startTime}.gpx`, this.results.locationResult)
        .file(`accelerometer-${this.startTime}.json`, this.results.accelerometerResult);
      zip.generateAsync({type: "blob"})
        .then((blob) => {
          // window.saveAs(blob, `measurements-${this.startTime}.zip`);
          this.zipUrl = URL.createObjectURL(blob);
        });
    }
  }
}
</script>
