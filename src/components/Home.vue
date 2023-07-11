<script setup lang="ts">
const items = [
  {
    "title": "Wohnzimmer",
    "devices": 4
  },
  {
    "title": "Schlafzimmer",
    "devices": 2
  }
];
</script>

<template>
<v-container class="fill-height">
  <v-responsive class="align-center text-center fill-height">
    <div class="pa-2">
      <h1>Hallo, Kai Käfer!</h1>
    </div>
    <div class="align-center">
      <v-list lines="one">
        <v-list-item
          v-for="item in items"
          :key="item.title"
          :title="item.title"
          :subtitle="item.devices+' Geräte'"
        ></v-list-item>
      </v-list>
    </div>
  </v-responsive>
</v-container>

</template>

<style scoped>

</style>
