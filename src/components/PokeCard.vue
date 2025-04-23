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
        <v-card-title>{{ capitalizeStr(pokemon?.name) }}</v-card-title>
        <v-card-subtitle>
          <span v-for="{ type, slot } in pokemon?.types" :key="slot">
            {{ capitalizeStr(type.name) }}
            <span v-if="slot < pokemon?.types.length">• </span>
          </span>
        </v-card-subtitle>
        <v-list density="compact">
          <v-list-group value="Abilities">
            <template #activator="{ props }">
              <v-list-item v-bind="props" title="Abilities"></v-list-item>
            </template>
            <v-list-item
              v-for="{ ability, is_hidden, slot } in pokemon?.abilities"
              :key="slot"
            >
              {{ capitalizeStr(ability.name) }}
              {{ is_hidden ? "(Hidden)" : "" }}
            </v-list-item>
          </v-list-group>
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
import { capitalizeStr } from "@/utils";

const props = defineProps({
  url: String,
  name: String,
});

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
      .catch((error) => {
        console.error("🚩 Error getting Pokemon:", error);
      });
  }
};
</script>
