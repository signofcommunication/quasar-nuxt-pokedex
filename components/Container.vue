<script setup lang="ts">
import { ref, onMounted } from 'vue';

const { data } = await useFetch('https://pokeapi.co/api/v2/pokemon?limit=151');

let result = ref([]);
let pokemonList = ref([]);

onMounted(() => {
  if (data.value?.results) {
    pokemonList.value = data.value.results.map((pokemon) => ({
      name: pokemon.name,
      imageUrl: `https://img.pokemondb.net/artwork/large/${pokemon.name}.jpg`,
    }));
  }
});
</script>

<template>
  <div class="q-pa-md">
    <div v-for="pokemon in pokemonList" :key="pokemon.name">
      <p>{{ pokemon.name }}</p>
      <img
        :src="pokemon.imageUrl"
        :alt="pokemon.name"
        width="100"
        height="100"
      />
    </div>
  </div>
</template>
