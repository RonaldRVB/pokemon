<template>
  <div class="min-h-screen bg-gray-100 p-4">
    <h1 class="text-4xl font-bold text-center text-gray-800 mb-6">Pokédex Interactif</h1>

    <div class="max-w-7xl mx-auto">
      <div class="flex flex-col sm:flex-row justify-between items-center mb-6">
        <input
          type="text"
          v-model="searchQuery"
          placeholder="Rechercher un Pokémon..."
          class="p-2 border border-gray-300 rounded bg-white text-gray-800 placeholder-gray-400 w-full sm:w-64 mb-4 sm:mb-0"
        />
        <TypeFilter
          :types="types"
          :selectedType="selectedType"
          @update:selectedType="selectedType = $event"
        />
      </div>

      <PokemonList :pokemons="filteredPokemons" @select="showDetail" />
    </div>

    <div v-if="selectedPokemon" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 p-4">
      <PokemonDetail :pokemon="selectedPokemon" @close="closeDetail" />
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

    const fetchPokemons = async () => {
      try {
        const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=151');
        const data = await response.json();
        pokemons.value = await Promise.all(data.results.map(async (pokemon, index) => {
          const detailResponse = await fetch(pokemon.url);
          const detailData = await detailResponse.json();
          const speciesResponse = await fetch(detailData.species.url);
          const speciesData = await speciesResponse.json();
          const evolutionChainResponse = await fetch(speciesData.evolution_chain.url);
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
      } catch (error) {
        console.error("Erreur lors de la récupération des données:", error);
      }
    };

    const fetchTypes = async () => {
      try {
        const response = await fetch('https://pokeapi.co/api/v2/type/');
        const data = await response.json();
        types.value = data.results;
      } catch (error) {
        console.error("Erreur lors de la récupération des types:", error);
      }
    };

    const filteredPokemons = computed(() => {
      return pokemons.value.filter(pokemon => {
        const matchesSearch = pokemon.name.toLowerCase().includes(searchQuery.value.toLowerCase());
        const matchesType = selectedType.value ? pokemon.types.some(type => type.type.name === selectedType.value) : true;
        return matchesSearch && matchesType;
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
      showDetail,
      closeDetail,
      filteredPokemons,
    };
  },
}
</script>
