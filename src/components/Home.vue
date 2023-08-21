<script setup lang="ts">
 async function startTvEvening() {
   try {
     await fetch(`http://192.168.178.21:8088/set/hmip.0.devices.3014F711A0002018A99EC715.channels.1.setPointTemperature?value=20`);
     await fetch(`http://192.168.178.21:8088/set/denon.0.zoneMain.powerZone?value=true`);
   } catch (e) {
     console.log(e);
   }
 }

 async function startGoodNight() {
   try {
     await fetch(`http://192.168.178.21:8088/set/hmip.0.devices.3014F711A0002018A99EC715.channels.1.setPointTemperature?value=5`);
     await fetch(`http://192.168.178.21:8088/set/denon.0.zoneMain.powerZone?value=false`);
     await fetch(`http://192.168.178.21:8088/set/hmip.0.devices.3014F711A00001D3C99C6C58.channels.1.on?value=true`);
   } catch (e) {
     console.log(e);
   }
 }
</script>

<template>
<v-container class="fill-height">
  <v-responsive class="align-center text-center fill-height">
    <div class="pa-2">
      <h1>Hallo, Kai Käfer!</h1>
    </div>
    <div class="align-center">
      <v-list>
        <v-list-item>
          <v-btn color="primary" @click="startTvEvening">Fernsehabend</v-btn>
        </v-list-item>
        <v-list-item>
          <v-btn color="primary" @click="startGoodNight">Gute Nacht</v-btn>
        </v-list-item>
      </v-list>
    </div>
  </v-responsive>
</v-container>

</template>

<style scoped>

</style>
