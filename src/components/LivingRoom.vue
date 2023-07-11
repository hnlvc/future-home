<script setup lang="ts">
import {onMounted, ref} from "vue";

const currentTemp = ref('')
const denonStatus = ref('')
const robotIsCleaning = ref('')

onMounted(async () => {
  try {
    const temperatureResponse = await fetch("http://192.168.178.21:8088/getPlainValue/hmip.0.devices.3014F711A0002018A99EC715.channels.1.valveActualTemperature")
    if (temperatureResponse.ok) {
      const data = await temperatureResponse.text()
      currentTemp.value = data + '°C'
    } else {
      console.error('Failed to fetch temperature data')
    }
  } catch (error) {
    console.error('An error occurred:', error)
  }
  try {
    const denonResponse = await fetch("http://192.168.178.21:8088/getPlainValue/denon.0.zoneMain.powerZone")
    if (denonResponse.ok) {
      const responseText = await denonResponse.text();
      if (responseText === "true") {
        denonStatus.value = "ON";
      } else {
        denonStatus.value = "OFF";
      }
    }
  } catch (error) {
    console.error('An error occurred:', error);
  }
  try {
    const robotResponse = await fetch("http://192.168.178.21:8088/getPlainValue/mihome-vacuum.0.control.clean_home")
    if (robotResponse) {
      const responseText = await robotResponse.text();
      if (responseText === "true") {
        robotIsCleaning.value = "Ja";
      } else {
        robotIsCleaning.value = "Nein";
      }
    }
  } catch (error) {
    console.error('An error occurred:', error);
  }

  //http://192.168.178.21:8088/getPlainValue/mihome-vacuum.0.control.clean_home

})
</script>

<template>
  <v-container class="fill-height">
    <v-responsive class="align-center text-center fill-height">
      <div class="pa-2">
        <h1>Wohnzimmer</h1>
      </div>
      <div class="align-center">
        <v-list lines="one">
          <v-list-item
            title="Aktuelle Temperatur"
            :subtitle="currentTemp"
          ></v-list-item>
          <v-list-item
            title="DENON AVR-X1100W"
            :subtitle="denonStatus"
          ></v-list-item>
          <v-list-item
            title="Staubsauger-Roboter"
            :subtitle="'Reinigt gerade: '+robotIsCleaning"
          ></v-list-item>
        </v-list>
      </div>
    </v-responsive>
  </v-container>
</template>

<style scoped>
/* Add any necessary styles here */
</style>
