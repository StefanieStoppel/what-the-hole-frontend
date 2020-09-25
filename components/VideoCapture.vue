<template>
  <v-container fluid>
    <h2 class="text-center">Video recording</h2>
    <v-row>
      <v-col cols="6">
        <vue-record-video ref="videoRecorder" :mode="'press'" @result="onResult"/>
      </v-col>
      <v-col cols="6">
        <v-btn v-if="recordedVideoFile != null"
               ref="downloadButton"
               :href="recordedVideoFile"
               :download="recordedVideoFileName"
        >Download
        </v-btn>
      </v-col>
    </v-row>
    <v-row v-if="recordedVideoFile != null">
      <v-col cols="12">
        <video ref="videoPlayer" controls :src="recordedVideoFile"></video>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "VideoCapture",
  data() {
    return {
      recordedVideoFile: null
    }
  },
  mounted() {
    this.updateButtonStyles();
  },
  computed: {
    recordedVideoFileName() {
      return `video-${Date.now()}.webm`
    }
  },
  methods: {
    onResult(data) {
      console.log('The blob data:', data);
      const videoBlob = URL.createObjectURL(data);
      console.log('Downloadable audio', videoBlob);
      this.recordedVideoFile = videoBlob;
    },
    updateButtonStyles () {
      window.setTimeout(() => {
        this.$refs.videoRecorder.$el.firstElementChild.classList.remove('recorder-icon');
        this.$refs.videoRecorder.$el.firstElementChild.classList.add('v-btn', 'v-btn--contained', 'theme--dark', 'v-size--default');
      }, 50);
    }
  }
}
</script>

<style>
</style>
