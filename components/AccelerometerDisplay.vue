<template>
  <v-container fluid>
    <h2>Acceleration</h2>
    <v-row>
      <v-col sm="12" md="6">
        <strong>Status:</strong> {{recording ? 'recording' : '-'}}
      </v-col>
    </v-row>
    <v-row >
      <v-col sm="12" md="4"
             v-for="[axis, accelerationOnAxis] in Object.entries(currentAcceleration.getAccelerationDisplay())"
             :key="axis">
            {{ axis }}: {{ accelerationOnAxis }} m/s&sup2;
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-card-title>Sorry, your device does not include an accelerometer! :(</v-card-title>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
class TimestampedAcceleration {
  constructor(x, y, z, time) {
    this.x = x;
    this.y = y;
    this.z = z;
    this.time = time;
  }
  getAccelerationDisplay() {
    return {x: this.x, y: this.y, z: this.z}
  }
  toJSONObject() {
    return {...this.getAccelerationDisplay(), time: this.time}
  }
}

export default {
  name: "AccelerometerDisplay",
  data() {
    return {
      recording: false,
      accelerometer: null,
      measurementFrequency: 5,
      currentAcceleration: new TimestampedAcceleration(null, null, null,null),
      accelerationMeasurements: [],
      measurementCounter: 0
    }
  },
  mounted() {
    this.initAccelerometer()
  },
  methods: {
    initAccelerometer() {
      this.accelerometer = new window.Accelerometer({frequency: this.measurementFrequency}) || null;
      // if (!!this.accelerometer && this.accelerometerActivated) {
      //}
    },
    start () {
      this.recording = true;
      window.addEventListener("devicemotion", this.addMeasurement, true);
      this.createFakeEvents();
      console.log("Starting acceleration measurements...")
    },
    stop () {
      this.recording = false;
      window.removeEventListener('devicemotion', this.addMeasurement, true)
      console.log("Stopping acceleration measurments...")
      const accJSON = JSON.stringify(this.accelerationMeasurements)
      const accelerationBlob = new Blob([accJSON], {type: 'application/json'});
      this.$emit('accelerometer-result', accelerationBlob)
    },
    addMeasurement(event) {
      const acc = event.acceleration;
      this.currentAcceleration = new TimestampedAcceleration(acc.x, acc.y, acc.z, this.getCurrentISOTimestamp());
      this.accelerationMeasurements.push(this.currentAcceleration.toJSONObject());
    },
    getCurrentISOTimestamp() {
      return new Date().toISOString();
    },
    createFakeEvents() {
      setInterval(() => {
        const deviceMotionEvent = new DeviceMotionEvent("devicemotion",{
          acceleration: {x: Math.random(), y: Math.random(), z: Math.random()}
        });
        window.dispatchEvent(deviceMotionEvent)
      }, 200)

    }
  },
  computed: {
    accelerometerActivated() {
      return this.accelerometer ? this.accelerometer.activated : false
    }
  }
}
</script>

<style scoped>

</style>
