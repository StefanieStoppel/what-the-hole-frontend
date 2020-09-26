<template>
  <v-container fluid>
    <h2 class="text-center">Video recording</h2>
    <v-row>
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
      console.log("Video started");
      this.$refs.videoRecorder.record()
    },
    stop () {
      console.log("Video stopped");
      this.$refs.videoRecorder.stop()
    },
    onResult() {
      const videoBlob = this.$refs.videoRecorder.media.blob;
      console.log('The videoBlob:', videoBlob);
      this.$emit('video-result', videoBlob);
      this.$refs.videoRecorder.reset()
    }
  }
}
</script>

<style>
</style>
