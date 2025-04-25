<template>
  <v-list-group :value="mainKey">
    <template #activator="{ props }">
      <v-list-item v-bind="props" :title="title ?? mainKey"></v-list-item>
    </template>
    <v-list-item
      class="text-capitalize"
      v-for="(item, index) in list"
      :key="index"
    >
      {{ item?.[mainKey]?.[subkey] }}
    </v-list-item>
  </v-list-group>
</template>

<script setup>
const props = defineProps({
  list: Array,
  mainKey: String,
  subkey: {
    type: String,
    default: "name",
  },
  title: {
    type: [String, null],
    default: null,
  },
});
</script>
