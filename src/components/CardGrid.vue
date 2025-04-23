<template>
  <v-container>
    <v-pagination
      v-model="page"
      :length="totalPages"
      total-visible="5"
    ></v-pagination>
    <v-row class="justify-center">
      <v-col
        v-for="(p, index) in isLoading ? 24 : pokeList"
        :key="index"
        cols="12"
        sm="6"
        md="4"
        lg="3"
      >
        <v-skeleton-loader
          type="list-item"
          :loading="isLoading"
          class="justify-center"
        >
          <h2 class="text-subtitle my-2">
            {{ capitalizeStr(p?.name) }}
          </h2>
          <PokeCard :url="p?.url" :name="p?.name" />
        </v-skeleton-loader>
      </v-col>
    </v-row>
  </v-container>
  <v-snackbar v-model="showSnackbar" color="error">
    Error fetching Pokemon
    <template #actions>
      <v-btn variant="text" @click="showSnackbar = false"> Close </v-btn>
    </template>
  </v-snackbar>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";
import axios from "axios";
import { ENDPOINT, LIMIT } from "@/constants";
import { capitalizeStr } from "@/utils";

const isLoading = ref(true);
const showSnackbar = ref(false);
const page = ref(0);
const totalPages = ref(1);
const pokeList = ref([]);

onMounted(() => {
  page.value = 1;
});

watch(page, (newPage) => {
  isLoading.value = true;
  const offset = (newPage - 1) * LIMIT;
  const url = `${ENDPOINT.POKEMON}?limit=${LIMIT}&offset=${offset}`;
  axios
    .get(url)
    .then(({ data }) => {
      pokeList.value = data?.results ?? [];
      isLoading.value = false;
      totalPages.value = Math.ceil(data.count / LIMIT);
    })
    .catch((error) => {
      console.error("🚩 Error getting list of Pokemon:", error);
      showSnackbar.value = true;
    });
});
</script>
