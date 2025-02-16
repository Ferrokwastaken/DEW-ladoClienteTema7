<script setup>
import { defineProps, computed } from 'vue'

const props = defineProps({
  country: {  
    type: Object,
    default: null}
})

const gdpPerCapita = computed(() => {
  if (props.country && props.country.popuplation && props.country.gdp) {
    return props.country.gdp / props.country.population
  }
  return null
})

const isGdpAboveIncome = computed(() => {
  if (gdpPerCapita.value && props.country && props.country.income) {
    return gdpPerCapita.value > props.country.income
  }
  return false
})
</script>

<template>
<div v-if="props.country">
  <p>Nombre: {{ props.country.name }}</p>
  <p>Población: {{ props.country.population }}</p>
  <p>Ingreso medio: {{ props.country.income }}</p>
  <p>PIB: </p>
  <p>Área: </p>
  <p :style="{color: isGdpAboveIncome ? 'red' : 'black'}">PIB per cápita: {{ gdpPerCapita }}</p>
</div>
<div>
  <p>No se pueden mostrar los datos del país</p>
</div>
</template>

<style scoped>

</style>