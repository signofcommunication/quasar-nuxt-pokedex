<script setup lang="ts">
import { ref, watchEffect } from 'vue';

const current = ref(1);
const pokemonList = ref<{ name: string; imageUrl: string }[]>([]);
const isLoading = ref(true);
const search = ref('');

interface Pokemon {
  name: string;
  imageUrl: string;
}

interface PokemonResponse {
  results: Pokemon[];
}

interface PokemonDetails {
  name: string;
}

// Fetch paginated Pokémon list when `current` changes
watchEffect(async () => {
  isLoading.value = true;

  const { data } = await useFetch<PokemonResponse>(`https://pokeapi.co/api/v2/pokemon/?limit=40&offset=${(current.value - 1) * 40}`);

  if (data.value?.results) {
    pokemonList.value = data.value.results.map((pokemon: Pokemon) => ({
      name: pokemon.name,
      imageUrl: `https://img.pokemondb.net/artwork/large/${pokemon.name}.jpg`,
    }));
  }

  isLoading.value = false;
});

// Function to search Pokémon by name
async function getPokemonByName() {
  if (!search.value) return;

  isLoading.value = true;

  // Fetch Pokémon by name
  const { data } = await useFetch<PokemonDetails>(`https://pokeapi.co/api/v2/pokemon/${search.value.toLowerCase()}`);

  if (data.value) {
    pokemonList.value = [
      {
        name: data.value.name,
        imageUrl: `https://img.pokemondb.net/artwork/large/${data.value.name}.jpg`,
      },
    ];
  } else {
    pokemonList.value = []; // Clear the list if no Pokémon is found
  }

  isLoading.value = false;
}
</script>

<template>
  <section class="q-pa-md">
    <div class="flex items-center justify-center space-x-2 p-4">
      <div class="w-full max-w-xs">
        <q-input
          square
          outlined
          v-model="search"
          label="Search Pokemon"
          class="w-full"
        />
      </div>
      <q-btn @click="getPokemonByName" color="primary" label="Search" />
    </div>

    <!-- Display loader while data is being fetched -->
    <div v-if="isLoading" class="text-center my-4">
      <q-spinner-orbit color="primary" size="10em" />
    </div>

    <!-- Display Pokémon list once data is loaded -->
    <div v-else class="flex items-center justify-center">
      <div v-if="pokemonList.length === 0">
        <p>
          No pokemon with name <b>{{ search }}</b>
        </p>
      </div>
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
      <q-pagination v-model="current" :max="10" />
    </div>
  </section>
</template>
