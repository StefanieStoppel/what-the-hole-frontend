<template>
  <v-container fluid>
    <h2 class="text-center">Acceleration</h2>
    <v-row>
      <v-col cols="12">
        <v-row
          align="center"
          justify="center"
          style="height: 300px;"
        >
          <v-card
            v-for="[axis, accelerationOnAxis] in Object.entries(acceleration)"
            :key="axis"
            class="ma-3 pa-6"
            outlined
            tile
          >
            <v-card-title>{{ axis }}</v-card-title>
            {{ accelerationOnAxis }} m/s&sup2;
          </v-card>
        </v-row>
        <v-row>
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
      accelerometer: null,
      updateFrequency: 5,
      acceleration: {x: null, y: null, z: null}
    }
  },
  mounted() {
    this.accelerometer = new window.Accelerometer({frequency: this.updateFrequency}) || null;
    if (!!this.accelerometer && this.accelerometerActivated) {
      this.addAccelerometerListener();
      console.log(this.accelerometer);
    }
  },
  methods: {
    addAccelerometerListener() {
      this.accelerometer.addEventListener('reading', () => {
        this.updateAcceleration()
      });
      this.accelerometer.start();
    },
    updateAcceleration() {
      this.acceleration.x = this.accelerometer.x;
      this.acceleration.y = this.accelerometer.y;
      this.acceleration.z = this.accelerometer.z;
      console.log("Acceleration along the X-axis " + this.accelerometer.x);
      console.log("Acceleration along the Y-axis " + this.accelerometer.y);
      console.log("Acceleration along the Z-axis " + this.accelerometer.z);
    }
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
