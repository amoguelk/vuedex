<template>
  <v-container
    class="fill-height text-center flex-column"
    :width="mdAndUp ? '75vw' : '90vw'"
  >
    <div>
      <h1 class="text-h1">VueDex</h1>
      <h2 class="text-subtitle-1">Your Vue.js Pokédex</h2>
    </div>
    <CardGrid />
  </v-container>
</template>

<script setup>
import { useDisplay } from "vuetify";

const { mdAndUp } = useDisplay();
</script>
