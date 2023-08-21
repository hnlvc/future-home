<script setup lang="ts">
import { onMounted, ref, computed } from "vue";

// Temperature variables
const currentTemp = ref('');
const desiredTemp = ref('');
const newDesiredTemp = ref(0);
const desiredTemperatureDialog = ref(false);

// Denon variables
const denonStatus = ref('');
const avrDialog = ref(false);
const desiredDenonStatus = ref(false);

// Robot variables
const robotIsCleaning = ref('');
const robotDialog = ref(false);
const shouldRobotClean = ref(false);

async function fetchData(url: string) {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error('Failed to fetch data');
    }
    return await response.text();
  } catch (error) {
    console.error('An error occurred:', error);
    throw error;
  }
}

async function getTemperatureData() {
  try {
    const currentTempData = await fetchData("http://192.168.178.21:8088/getPlainValue/hmip.0.devices.3014F711A0002018A99EC715.channels.1.valveActualTemperature");
    currentTemp.value = currentTempData.toString();

    const desiredTempData = await fetchData("http://192.168.178.21:8088/getPlainValue/hmip.0.devices.3014F711A0002018A99EC715.channels.1.setPointTemperature");
    desiredTemp.value = desiredTempData.toString();
    newDesiredTemp.value = parseFloat(desiredTempData);
  } catch (error) {
    console.error('Error while fetching temperature data:', error);
  }
}

async function getDenonStatus() {
  try {
    const denonResponse = await fetchData("http://192.168.178.21:8088/getPlainValue/denon.0.zoneMain.powerZone");
    denonStatus.value = denonResponse === "true" ? "ON" : "OFF";
  } catch (error) {
    console.error('Error while fetching DENON status:', error);
  }
}

async function getRobotStatus() {
  try {
    const robotResponse = await fetchData("http://192.168.178.21:8088/getPlainValue/mihome-vacuum.0.control.clean_home");
    robotIsCleaning.value = robotResponse === "true" ? "Ja" : "Nein";
  } catch (error) {
    console.error('Error while fetching robot status:', error);
  }
}

async function setTemperature(value: number) {
  desiredTemperatureDialog.value = false;
  desiredTemp.value = value.toString();

  try {
    await fetch(`http://192.168.178.21:8088/set/hmip.0.devices.3014F711A0002018A99EC715.channels.1.setPointTemperature?value=${value}`);
  } catch (e) {
    console.error('Error while setting temperature value: ', e);
  }
}

async function changeAvrState(desired: boolean) {
  avrDialog.value = false;
  try {
    const apiValue = desired ? 'true' : 'false';
    await fetch(`http://192.168.178.21:8088/set/denon.0.zoneMain.powerZone?value=${apiValue}`);

    denonStatus.value = desired ? "ON" : "OFF";
  } catch (e) {
    console.error('Error while switching AVR power: ', e);
  }
}

async function letRobotClean(desired: boolean) {
  robotDialog.value = false;
  try {
    const apiValue = desired ? 'true' : 'false';
    await fetch(`http://192.168.178.21:8088/set/mihome-vacuum.0.control.clean_home?value=${apiValue}`);

    robotIsCleaning.value = desired;
  } catch (e) {
    console.error('Error while switching cleaning status: ', e);
  }
}

onMounted(async () => {
  await Promise.all([
    getTemperatureData(),
    getDenonStatus(),
    getRobotStatus(),
  ]);
});

const formattedCurrentTemp = computed(() => `${currentTemp.value}°C`);
const formattedDesiredTemp = computed(() => `${desiredTemp.value}°C`);
</script>

<template>
  <v-container class="fill-height">
    <v-responsive class="align-center text-center fill-height">
      <div class="pa-2">
        <h1>Wohnzimmer</h1>
      </div>
      <div class="align-center">
        <v-list lines="one">
          <v-list-item>
            <v-list-item-title>Aktuelle Temperatur</v-list-item-title>
            <v-list-item-subtitle>{{ formattedCurrentTemp }}</v-list-item-subtitle>
          </v-list-item>
          <v-list-item>
            <v-list-item-title>Gewünschte Temperatur</v-list-item-title>
            <v-list-item-subtitle @click="desiredTemperatureDialog = true">{{ formattedDesiredTemp }}</v-list-item-subtitle>
          </v-list-item>
          <v-list-item>
            <v-list-item-title>DENON AVR-X1100W</v-list-item-title>
            <v-list-item-subtitle @click="avrDialog = true">{{ denonStatus }}</v-list-item-subtitle>
          </v-list-item>
          <v-list-item>
            <v-list-item-title>Staubsauger-Roboter</v-list-item-title>
            <v-list-item-subtitle @click="robotDialog = true" >Reinigt gerade: {{ robotIsCleaning }}</v-list-item-subtitle>
          </v-list-item>
        </v-list>
      </div>
    </v-responsive>
  </v-container>

  <v-dialog v-model="desiredTemperatureDialog">
    <v-card>
      <v-container class="dialog-slider-container" fill-height>
        <v-row justify="center" align="center" class="fill-height">
          <v-col cols="12">
            <v-slider
              v-model="newDesiredTemp"
              min="5"
              max="30"
              :step="0.5"
              thumb-label="always"
              thumb-color="primary"
            ></v-slider>
          </v-col>
        </v-row>
      </v-container>
      <v-card-actions>
        <v-btn color="primary" @click="setTemperature(newDesiredTemp)">Speichern</v-btn>
        <v-btn color="secondary" @click="desiredTemperatureDialog = false">Abbruch</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-dialog v-model="avrDialog">
    <v-card>
      <v-switch
        color="primary"
        v-model="desiredDenonStatus"
        label="Einschalten?"
      ></v-switch>
      <v-card-actions>
        <v-btn color="primary" @click="changeAvrState(desiredDenonStatus)">Speichern</v-btn>
        <v-btn color="secondary" @click="avrDialog = false">Abbruch</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-dialog v-model="robotDialog">
    <v-card>
      <v-switch
        color="primary"
        v-model="shouldRobotClean"
        label="Einschalten?"
      ></v-switch>
      <v-card-actions>
        <v-btn color="primary" @click="letRobotClean(shouldRobotClean)">Speichern</v-btn>
        <v-btn color="secondary" @click="robotDialog = false">Abbruch</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<style>
.dialog-slider-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
</style>





