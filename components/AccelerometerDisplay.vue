<template>
  <v-container fluid>
    <h2 class="text-center">Acceleration</h2>
    <v-row>
      <v-col cols="12">
        <v-row
          v-if="accelerometerActivated"
          align="center"
          justify="center"
          style="height: 300px;"
        >
          <v-card
            v-for="(accelerationDirection, index) in accelerationVector"
            :key="accelerationDirectionLabels[index]"
            class="ma-3 pa-6"
            outlined
            tile
          >
            <v-card-title>{{ accelerationDirectionLabels[index]}}</v-card-title>
            {{ accelerationDirection }} m/s&sup2;
          </v-card>
        </v-row>
        <v-row v-else>
          <v-card>
            <v-card-title>Sorry, your device does not include an accelerometer! :(</v-card-title>
          </v-card>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "AccelerometerDisplay",
  data() {
    return {
      accelerationDirectionLabels: ['x', 'y', 'z'],
      accelerometer: null
    }
  },
  mounted() {
    this.accelerometer = new window.Accelerometer() || null
    console.log(this.accelerometer)
  },
  computed: {
    accelerationVector() {
      return this.accelerometer ? [this.accelerometer.x, this.accelerometer.y, this.accelerometer.z] : []
    },
    accelerometerActivated() {
      return this.accelerometer ? this.accelerometer.activated : false
    }
  }
}
</script>

<style scoped>

</style>
