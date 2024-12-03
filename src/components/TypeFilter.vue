<template>
  <div class="mb-4">
    <label for="type" class="text-gray-800 mr-2">Filtrer par type:</label>
    <select
      id="type"
      :value="selectedType"
      @change="$emit('update:selectedType', $event.target.value)"
      class="p-2 border border-gray-300 rounded bg-white text-gray-800"
    >
      <option value="">Tous</option>
      <option v-for="type in types" :key="type.name" :value="type.name">
        {{ type.name }}
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
}
</script>
