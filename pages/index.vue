<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="10">
      <video-capture/>
      <location-tracker/>
      <accelerometer-display/>
    </v-col>
  </v-row>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'
import AccelerometerDisplay from '~/components/AccelerometerDisplay'
import LocationTracker from "~/components/LocationTracker";
import VideoCapture from "~/components/VideoCapture";

export default {
  components: {
    LocationTracker,
    VideoCapture,
    Logo,
    VuetifyLogo,
    AccelerometerDisplay
  }
}
</script>
