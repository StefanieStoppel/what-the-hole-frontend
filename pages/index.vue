<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="10">
      <accelerometer-display/>
    </v-col>
  </v-row>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'
import AccelerometerDisplay from '~/components/AccelerometerDisplay'

export default {
  components: {
    Logo,
    VuetifyLogo,
    AccelerometerDisplay
  }
}
</script>
