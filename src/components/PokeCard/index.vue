<template>
  <v-dialog
    activator="parent"
    max-width="900"
    @update:model-value="handleDialogOpen"
  >
    <template #default="{ isActive }">
      <v-card :loading="isLoading">
        <v-img
          height="300"
          :src="pokemon?.sprites?.front_default"
          position="top center"
        />
        <v-card-title class="text-capitalize">{{ pokemon?.name }}</v-card-title>
        <v-card-subtitle>
          <span
            class="text-capitalize"
            v-for="({ type }, index) in pokemon?.types"
            :key="index"
          >
            {{ type.name }}
            <span v-if="index < pokemon?.types.length - 1">• </span>
          </span>
        </v-card-subtitle>
        <v-list density="compact">
          <FeatureList
            v-for="{ listKey, mainKey, title, subkey } in features"
            :list="pokemon?.[listKey]"
            :mainKey="mainKey"
            :title="title"
            :subkey="subkey"
          />
        </v-list>
        <v-card-actions>
          <v-btn @click="isActive.value = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </template>
  </v-dialog>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const props = defineProps({
  url: String,
  name: String,
  setShowSnackbar: Function,
});

const features = [
  { listKey: "abilities", mainKey: "ability", title: "Abilities" },
  { listKey: "moves", mainKey: "move", title: "Moves" },
];

const pokemon = ref(null);
const isLoading = ref(true);

const handleDialogOpen = (isOpen) => {
  if (isOpen) {
    isLoading.value = true;
    axios
      .get(props.url)
      .then(({ data }) => {
        pokemon.value = data ?? null;
        isLoading.value = false;
      })
      .catch(({ response }) => {
        console.error("🚩 Error getting Pokemon:", response);
        props.setShowSnackbar(true, response);
      });
  }
};
</script>
