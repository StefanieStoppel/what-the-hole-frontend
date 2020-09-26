<template>
  <v-container fluid>
    <h2 class="text-center">Video recording</h2>
    <v-row v-show="false">
      <v-col cols="6">
        <vue-record-video ref="videoRecorder" :mode="'press'" @result="onResult"/>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "VideoCapture",
  methods: {
    start () {
      console.log("Video started")
      this.$refs.videoRecorder.start()
    },
    stop () {
      console.log("Video stopped")
      this.$refs.videoRecorder.stop()
    },
    onResult(videoBlob) {
      console.log('The blob videoBlob:', videoBlob);
      const videoBlobUrl = URL.createObjectURL(videoBlob);
      console.log('Downloadable video', videoBlobUrl);
      this.$emit('video-result', videoBlob)
    }
  }
}
</script>

<style>
</style>
