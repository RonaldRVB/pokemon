<template>
  <div class="w-full sm:w-auto">
    <label for="type" class="text-gray-700 mr-2">Filtrer par type:</label>
    <select
      id="type"
      :value="selectedType"
      @change="$emit('update:selectedType', $event.target.value)"
      class="p-2 border border-gray-300 rounded bg-white text-gray-800 w-full sm:w-auto"
    >
      <option value="">Tous</option>
      <option v-for="type in translatedTypes" :key="type.name" :value="type.name">
        {{ type.frenchName }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: 'TypeFilter',
  props: {
    types: Array,
    selectedType: String,
  },
  emits: ['update:selectedType'],
  computed: {
    translatedTypes() {
      const translations = {
        normal: 'Normal',
        fire: 'Feu',
        water: 'Eau',
        electric: 'Électrik',
        grass: 'Plante',
        ice: 'Glace',
        fighting: 'Combat',
        poison: 'Poison',
        ground: 'Sol',
        flying: 'Vol',
        psychic: 'Psy',
        bug: 'Insecte',
        rock: 'Roche',
        ghost: 'Spectre',
        dragon: 'Dragon',
        dark: 'Ténèbres',
        steel: 'Acier',
        fairy: 'Fée'
      };

      return this.types.map(type => ({
        ...type,
        frenchName: translations[type.name] || type.name
      }));
    }
  }
}
</script>

<style scoped>
/* Vous pouvez ajouter des styles spécifiques ici si nécessaire */
</style>
