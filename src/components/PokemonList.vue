<template>
  <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 p-4">
    <div
      v-for="pokemon in pokemons"
      :key="pokemon.id"
      @click="$emit('select', pokemon)"
      class="rounded-lg shadow-md p-4 text-center bg-white text-gray-800 cursor-pointer hover:bg-gray-100 transition-colors duration-200"
    >
      <img
        :src="pokemon.sprites.front_default"
        :alt="pokemon.name"
        class="w-24 h-24 mx-auto"
      />
      <h2 class="text-lg font-semibold mt-2">{{ pokemon.name }}</h2>
      <div class="mt-2 text-sm">
        <span
          v-for="type in pokemon.types"
          :key="type.type.name"
          class="inline-block px-2 py-1 mr-1 mb-1 rounded-full text-xs text-white"
          :class="getTypeColor(type.type.name)"
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
    pokemons: {
      type: Array,
      required: true
    }
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
