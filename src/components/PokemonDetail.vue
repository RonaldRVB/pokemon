<template>
  <div class="bg-white rounded-lg shadow-xl p-6 w-full max-w-lg max-h-[90vh] overflow-y-auto">
    <div class="flex justify-between items-start mb-4">
      <h2 class="text-3xl font-bold text-gray-800">{{ pokemon.name }}</h2>
      <button @click="$emit('close')" class="text-gray-500 hover:text-gray-700">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
        </svg>
      </button>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
      <div>
        <img :src="pokemon.sprites.front_default" :alt="pokemon.name" class="w-full h-auto mb-4 mx-auto" />
        <p><strong>ID:</strong> {{ pokemon.id }}</p>
        <p><strong>Taille:</strong> {{ pokemon.height / 10 }} m</p>
        <p><strong>Poids:</strong> {{ pokemon.weight / 10 }} kg</p>
        <p><strong>Types:</strong> {{ pokemon.types.map(type => type.type.name).join(', ') }}</p>
      </div>

      <div>
        <h3 class="text-xl font-semibold mb-2">Statistiques:</h3>
        <ul>
          <li v-for="stat in pokemon.stats" :key="stat.stat.name">
            {{ stat.stat.name }}: {{ stat.base_stat }}
          </li>
        </ul>
      </div>
    </div>

    <div v-if="pokemon.evolutions" class="mt-6">
      <h3 class="text-xl font-semibold mb-2">Évolutions:</h3>
      <div class="flex flex-wrap gap-4 justify-center">
        <div v-for="evolution in getEvolutions(pokemon.evolutions)" :key="evolution.species.name" class="text-center">
          <img :src="getEvolutionSprite(evolution)" :alt="evolution.species.name" class="w-24 h-24 mx-auto" />
          <p>{{ evolution.species.name }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PokemonDetail',
  props: {
    pokemon: {
      type: Object,
      required: true
    }
  },
  emits: ['close'],
  methods: {
    getEvolutions(chain) {
      let evolutions = [];
      let current = chain;

      while (current) {
        evolutions.push(current);
        current = current.evolves_to[0];
      }

      return evolutions;
    },

    getEvolutionSprite(evolution) {
      const id = evolution.species.url.split('/').slice(-2, -1)[0];
      return `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${id}.png`;
    }
  }
}
</script>

<style scoped>
/* Vous pouvez ajouter des styles spécifiques ici si nécessaire */
</style>
