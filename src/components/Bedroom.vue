<script setup lang="ts">
import { ref, onMounted } from 'vue';

const bedLampValue = ref('');
const shouldShowDialog = ref(false);
const shouldBeSwitchedOn = ref(false);

async function getBedLampValue() {
  const response = await fetch('http://192.168.178.21:8088/getPlainValue/hmip.0.devices.3014F711A00001D3C99C6C58.channels.1.on');
  bedLampValue.value = (await response.text()) === 'true' ? 'Eingeschaltet' : 'Ausgeschaltet';
}

async function changeLampStatus(value: boolean) {
  await fetch(`http://192.168.178.21:8088/set/hmip.0.devices.3014F711A00001D3C99C6C58.channels.1.on?value=${value.toString()}`);
}

onMounted(() => {
  getBedLampValue();
});
</script>

<template>
  <v-container class="fill-height">
    <v-responsive class="align-center text-center fill-height">
      <div class="pa-2">
        <h1>Schlafzimmer</h1>
      </div>
      <div class="align-center">
        <v-list lines="one">
          <v-list-item>
            <v-list-item-title>Bettlampe</v-list-item-title>
            <v-list-item-subtitle>{{ bedLampValue }}</v-list-item-subtitle>
          </v-list-item>
        </v-list>
      </div>
    </v-responsive>
  </v-container>

  <v-dialog v-model="shouldShowDialog">
    <v-card>
      <v-switch
        color="primary"
        v-model="shouldBeSwitchedOn"
        label="Einschalten?" />
      <v-card-actions>
        <v-btn color="primary" @click="changeLampStatus(shouldBeSwitchedOn)">Speichern</v-btn>
        <v-btn color="secondary" @click="shouldShowDialog = false">Abbruch</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<style scoped>
</style>
