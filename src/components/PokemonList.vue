<template>
  <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 xl:grid-cols-6 gap-4">
    <div
      v-for="pokemon in pokemons"
      :key="pokemon.id"
      @click="$emit('select', pokemon)"
      class="bg-white rounded-lg shadow-md p-4 text-center cursor-pointer hover:bg-gray-100 transition duration-300"
    >
      <img
        :src="pokemon.sprites.front_default"
        :alt="pokemon.name"
        class="w-full h-auto mx-auto"
      />

      <h2 class="text-lg font-semibold mt-2 text-gray-800">{{ pokemon.name }}</h2>

      <!-- Affichage des types -->
      <div class="mt-2 flex flex-wrap justify-center">
        <span
          v-for="type in pokemon.types"
          :key="type.type.name"
          class="inline-block px-2 py-1 text-xs text-white rounded-full mr-1 mb-1"
          :class="[getTypeColor(type.type.name)]"
        >
          {{ type.type.name }}
        </span>
     </div>
   </div>
  </div>
</template>

<script>
export default {
  name: 'PokemonList',
  props: {
    pokemons: Array
  },
  emits: ['select'],

  methods: {
     getTypeColor(type) {
       const colors = {
         normal: 'bg-gray-400',
         fire: 'bg-red-500',
         water: 'bg-blue-500',
         electric: 'bg-yellow-400',
         grass: 'bg-green-500',
         ice: 'bg-blue-200',
         fighting: 'bg-red-700',
         poison: 'bg-purple-500',
         ground: 'bg-yellow-600',
         flying: 'bg-indigo-400',
         psychic: 'bg-pink-500',
         bug: 'bg-green-400',
         rock: 'bg-yellow-700',
         ghost: 'bg-purple-700',
         dragon: 'bg-indigo-700',
         dark: 'bg-gray-700',
         steel: 'bg-gray-500',
         fairy: 'bg-pink-300'
       };
       return colors[type] || 'bg-gray-400';
     }
   }
}
</script>

<style scoped>
/* Vous pouvez ajouter des styles spécifiques ici si nécessaire */
</style>
