<template>
  <div class="flex flex-col items-center justify-start min-h-screen bg-gray-100 pt-8">
    <h1 class="text-4xl font-bold text-gray-800 mb-4">Pokédex Interactif</h1>

    <div class="flex items-center mb-4">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Rechercher un Pokémon..."
        class="p-2 border border-gray-300 rounded bg-white text-gray-800 placeholder-gray-400 w-64"
      />
      <button
        @click="searchQuery = ''"
        class="ml-2 p-2 bg-red-500 text-white rounded hover:bg-red-600"
      >
        Effacer
      </button>
    </div>

    <TypeFilter
      :types="types"
      :selectedType="selectedType"
      @update:selectedType="selectedType = $event"
      class="mb-4"
    />

    <PokemonList :pokemons="filteredPokemons" @select="showDetail" />

    <div v-if="selectedPokemon" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50">
      <PokemonDetail :pokemon="selectedPokemon" @close="closeDetail" />
    </div>

    <div v-if="error" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50">
      <div class="bg-white p-4 rounded-lg shadow-lg">
        <p class="text-red-500">{{ error }}</p>
        <button @click="error = ''" class="mt-2 p-2 bg-blue-500 text-white rounded hover:bg-blue-600">Fermer</button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue';
import PokemonList from './components/PokemonList.vue';
import PokemonDetail from './components/PokemonDetail.vue';
import TypeFilter from './components/TypeFilter.vue';

export default {
  name: 'App',
  components: {
    PokemonList,
    PokemonDetail,
    TypeFilter,
  },
  setup() {
    const pokemons = ref([]);
    const searchQuery = ref('');
    const selectedPokemon = ref(null);
    const selectedType = ref('');
    const types = ref([]);
    const error = ref('');

    const fetchPokemons = async () => {
      try {
        const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=151');
        if (!response.ok) throw new Error('Erreur lors de la récupération des Pokémon');
        const data = await response.json();
        pokemons.value = await Promise.all(data.results.map(async (pokemon, index) => {
          const detailResponse = await fetch(pokemon.url);
          if (!detailResponse.ok) throw new Error(`Erreur lors de la récupération des détails de ${pokemon.name}`);
          const detailData = await detailResponse.json();

          // Récupérer les évolutions
          const speciesResponse = await fetch(detailData.species.url);
          if (!speciesResponse.ok) throw new Error(`Erreur lors de la récupération des espèces de ${pokemon.name}`);
          const speciesData = await speciesResponse.json();
          const evolutionChainResponse = await fetch(speciesData.evolution_chain.url);
          if (!evolutionChainResponse.ok) throw new Error(`Erreur lors de la récupération de la chaîne d'évolution de ${pokemon.name}`);
          const evolutionChainData = await evolutionChainResponse.json();

          return {
            ...pokemon,
            id: index + 1,
            sprites: detailData.sprites,
            height: detailData.height,
            weight: detailData.weight,
            types: detailData.types,
            stats: detailData.stats,
            evolutions: evolutionChainData.chain,
          };
        }));
      } catch (err) {
        console.error(err);
        error.value = "Une erreur est survenue lors de la récupération des données. Veuillez réessayer plus tard.";
      }
    };

    const fetchTypes = async () => {
      try {
        const response = await fetch('https://pokeapi.co/api/v2/type/');
        if (!response.ok) throw new Error('Erreur lors de la récupération des types');
        const data = await response.json();
        types.value = data.results;
      } catch (err) {
        console.error(err);
        error.value = "Une erreur est survenue lors de la récupération des types. Veuillez réessayer plus tard.";
      }
    };

    const filteredPokemons = computed(() => {
      return pokemons.value.filter(pokemon => {
        const matchesSearch = pokemon.name.toLowerCase().includes(searchQuery.value.toLowerCase());
        const matchesType = selectedType.value ? pokemon.types.some(type => type.type.name === selectedType.value) : true;

        if (searchQuery.value) {
          return matchesSearch;
        }

        return matchesType;
      });
    });

    const showDetail = (pokemon) => {
      selectedPokemon.value = pokemon;
    };

    const closeDetail = () => {
      selectedPokemon.value = null;
    };

    onMounted(async () => {
      await fetchPokemons();
      await fetchTypes();
    });

    return {
      pokemons,
      searchQuery,
      selectedPokemon,
      selectedType,
      types,
      error,
      showDetail,
      closeDetail,
      filteredPokemons,
    };
  },
}
</script>
