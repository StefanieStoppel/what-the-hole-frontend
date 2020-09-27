<template>
  <v-container fluid>
    <h2>Video</h2>
    <v-row>
      <v-col sm="12" md="6">
        <strong>Status:</strong> {{recording ? 'recording' : '-'}}
      </v-col>
    </v-row>
    <v-row v-show="false">
      <v-col cols="6">
        <camera ref="videoRecorder"
                :requestAccess="true"
                :constraints="constraints"
                :facingMode="'environment'"
                :width="1200"
                :height="840"
                :audio="false"
                @status:stop="onResult">
          <div name="record"> Record video </div>
          <div name="stop"> Stop recording </div>
        </camera>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import {Camera} from 'vue-capture';

export default {
  name: "VideoCapture",
  components: {
    Camera
  },
  data () {
    return {
      recording: false,
      constraints: {
        audio: false,
        video: {
          facingMode: { exact: "environment" },
          width: { min: 1024, ideal: 1280, max: 1920 },
          height: { min: 576, ideal: 720, max: 1080 }
        }
      }
    }
  },
  methods: {
    start () {
      this.recording = true;
      console.log("Video started");
      // this.$refs.videoRecorder.record()
    },
    stop () {
      this.recording = false;
      console.log("Video stopped");
      // this.$refs.videoRecorder.stop()
    },
    onResult() {
      const videoBlob = this.$refs.videoRecorder.media.blob;
      console.log('The videoBlob:', videoBlob);
      this.$refs.videoRecorder.reset()
      this.$emit('video-result', videoBlob);
    }
  }
}
</script>

<style>
</style>
