<template>
  <v-container>
    <v-pagination
      v-model="page"
      :length="totalPages"
      total-visible="3"
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
          <div class="px-2 w-100 cursor-pointer">
            <h2 class="text-h6 text-capitalize text-truncate my-2">
              {{ p?.name }}
            </h2>
          </div>
          <PokeCard
            :url="p?.url"
            :name="p?.name"
            :setShowSnackbar="setShowSnackbar"
          />
        </v-skeleton-loader>
      </v-col>
    </v-row>
    <!-- Additional pagination at the bottom (if more than 25 items) -->
    <v-pagination
      v-if="!isLoading && pokeList.length >= LIMIT / 2"
      v-model="page"
      :length="totalPages"
      total-visible="3"
    ></v-pagination>
  </v-container>
  <v-snackbar v-model="showSnackbar" color="error" multi-line>
    <!-- Workaround for snackbar multiline issue https://github.com/vuetifyjs/vuetify/issues/15996 -->
    <div v-for="(line, i) in snackbarText">
      {{ line }}
      <span v-if="i < snackbarText.length - 1"><br /></span>
    </div>
    <template #actions>
      <v-btn variant="text" @click="setShowSnackbar(false)"> Close </v-btn>
    </template>
  </v-snackbar>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";
import axios from "axios";
import { ENDPOINT, ERRORS, LIMIT } from "@/constants";

const isLoading = ref(true);
const showSnackbar = ref(false);
const snackbarText = ref(["Error fetching Pokemon", ""]);
const page = ref(0);
const totalPages = ref(1);
const pokeList = ref([]);

const setShowSnackbar = (value, error = null) => {
  showSnackbar.value = value;
  snackbarText.value[1] =
    ERRORS?.[error?.status ?? error?.code] ?? ERRORS.UNKNOWN;
};

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
      console.error(
        "🚩 Error getting list of Pokemon:",
        error?.response ?? error,
      );
      setShowSnackbar(true, error?.response ?? error);
    });
});
</script>
