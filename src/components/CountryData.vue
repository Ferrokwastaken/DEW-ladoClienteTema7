<script setup>
import { defineProps, computed, defineEmits } from 'vue'

const props = defineProps({
  country: {  
    type: Object,
    default: null}
})

const emits = defineEmits(['delete-country'])

const gdpPerCapita = computed(() => {
  if (props.country && props.country.popuplation && props.country.pib) {
    return props.country.pib / props.country.population
  }
  return null
})

const isGdpAboveIncome = computed(() => {
  if (gdpPerCapita.value && props.country && props.country.income) {
    return gdpPerCapita.value > props.country.income
  }
  return false
})

const deleteCountryData = () => {
  if (props.country && props.country.code) {
    emits('delete-country', props.country.code)
  }
}
</script>

<template>
<div v-if="props.country">
  <table>
    <tr>
      <th>Nombre:</th>
      <td>{{ props.country.name }}</td>
      <th>Población</th>
      <td>{{ props.country.population }}</td>
    </tr>
    <tr>
      <th>PIB:</th>
      <td>{{ props.country.pib }}</td>
      <th>Área:</th>
      <td>{{ props.country.area }}</td>
    </tr>
    <tr>
      <th>Renta:</th>
      <td>{{ props.country.income }}</td>
      <th>PIB/habitante</th>
      <td :style="{ color: isGdpAboveIncome ? 'red' : 'black' }">{{ gdpPerCapita }}</td>
    </tr>
    <tr>
      <th>
        <button @click="deleteCountryData">Eliminar</button>
      </th>
    </tr>
  </table>
</div>
<div v-else>
  <h3>No hay datos que mostrar</h3>
</div>
</template>

<style scoped>

</style>