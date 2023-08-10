<template>
  <v-app id="inspire">
    <v-navigation-drawer app v-model="drawer">
      <v-list density="compact">
        <v-list-item v-for="item in items" :title="item.title" :to="item.to" :prepend-icon="item.icon" />
      </v-list>
    </v-navigation-drawer>

    <v-app-bar>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-toolbar-title>Future Home</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <router-view></router-view>
    </v-main>

  </v-app>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(null)
</script>

<script>
export default {
  data() {
    return {
      items: [
        { title: 'Home', icon: 'mdi-home', to: '/' },
        { title: 'Wohnzimmer', icon: 'mdi-sofa', to: '/livingroom' }
      ]
    };
  }
};
</script>
