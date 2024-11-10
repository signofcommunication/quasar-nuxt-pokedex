<script setup>
import { ref, watchEffect, onMounted } from 'vue';

const current = ref(2);
let pokemonList = ref([]);
const isLoading = ref(true);

watchEffect(async () => {
  isLoading.value = true;

  const { data } = await useFetch(`https://pokeapi.co/api/v2/pokemon/?limit=40&offset=${current.value}0`);

  console.log(current.value)

  if (data.value?.results) {
    pokemonList.value = data.value.results.map((pokemon) => ({
      name: pokemon.name,
      imageUrl: `https://img.pokemondb.net/artwork/large/${pokemon.name}.jpg`,
    }));
  }

  isLoading.value = false;
});
</script>

<template>
  <section class="q-pa-md">
    <div>
      <label
        for="small-input"
        class="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
      >
        Small Pokemon
      </label>
      <input
        type="text"
        id="small-input"
        class="block w-full p-2 text-gray-900 border border-gray-300 rounded-lg bg-gray-50 text-xs focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
      />
    </div>

    <!-- Display loader while data is being fetched -->
    <div v-if="isLoading" class="text-center my-4">Loading...</div>

    <!-- Display Pokémon list once data is loaded -->
    <div v-else class="flex items-center justify-center">
      <div
        v-for="pokemon in pokemonList"
        :key="pokemon.name"
        class="text-center my-4"
      >
        <p>{{ pokemon.name }}</p>
        <img
          :src="pokemon.imageUrl"
          :alt="pokemon.name"
          width="100"
          height="100"
        />
      </div>
    </div>

    <!-- Pagination control -->
    <div class="q-pa-lg flex flex-center">
      <q-pagination v-model="current" :max="5" />
    </div>
  </section>
</template>
