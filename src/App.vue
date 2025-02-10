<script setup>
import { computed, onMounted, ref } from 'vue'
import codes from './data/codes.json'

const name = ref('')
const selectedCodes = ref([])
const countryData = ref([])
const countryNames = ref([])

onMounted(async () => {
  try {
    const res = await fetch('http://localhost:3000/api/country/ESP')
    countryData.value = await res.json()
    console.log(countryData)
  } catch (error) {
    console.log('Error al recibir los datos', error)
  }
})

const selectCode = (code) => {
  console.log(code)
  const selectedCode = selectedCodes.value.includes(code)
  if (!selectedCode) {
    selectedCodes.value.push(code)
    console.log(selectedCodes.value)
  }
}

const unmark = (code) => {
  selectedCodes.value = selectedCodes.value.filter((c) => c !== code)
}

onMounted(async () => {
  try {
    const res = await fetch('http://localhost:3000/api/names')
    countryNames.value = await res.json()
  } catch (error) {
    console.log('Error al recibir los datos', error)
  }
})

// const sortCountries = computed(() => {
//   return countryNames.value.sort((a, b) => a.localeCompare(b))
// })
</script>

<template>
  <div class="parent">
    <div class="header">
      <h1>Datos mundiales</h1>
    </div>
    <div class="name">
      <p>Inserta tu nombre:</p>
      <input type="text" v-model="name"/>
      <p v-if="name.length > 0">Tu nombre <b>{{ name }}</b> tiene <b>{{ name.length }}</b> letras</p>
    </div>
    <div class="div-codes">
      <ul>
        <li v-for="countryName in countryNames" @click="selectCode(countryName)">
          {{ countryName }}
        </li>
      </ul>
    </div>
    <div class="selected">
      <h2 v-if="selectedCodes.length == 0">Debes seleccionar algún país</h2>
      <ul v-else>
        <li v-for="code in selectedCodes" :key="code">
          {{ code }}
          <button @click="unmark(code)">Desmarcar</button>
        </li>
      </ul>
    </div>
    <div class="country-data"></div>
    <div class="options"></div>
    <div class="chart"></div>
  </div>
</template>

<style scoped>
.parent {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: auto repeat(3, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
  height: 100vh;
  margin: -2rem;
}

.header {
  grid-area: 1 / 1 / 2 / 6;
  background-color: aquamarine;
}

.header h1 {
  margin: 2px;
}

.div-codes {
  grid-area: 2 / 1 / 6 / 2;
  background-color: azure;
  overflow-y: auto;
}

.name {
  grid-area: 2 / 2 / 3 / 3;
  background-color: antiquewhite;
}

.selected {
  grid-area: 2 / 5 / 4 / 6;
  background-color: lightyellow;
  overflow-y: auto;
}

.country-data {
  grid-area: 2 / 3 / 3 / 5;
  background-color: lightseagreen;
}

.options {
  grid-area: 3 / 2 / 4 / 5;
  background-color: bisque;
}

.chart {
  grid-area: 4 / 2 / 5 / 6;
  background-color: aqua;
}

ul li {
  text-align: left;
  font-size: 0.9rem;
}

ul li:hover {
  font-weight: bold;
  cursor: pointer
}

ul li button {
  font-size: 0.6rem;
  margin: 2px;
}

h2 {
  font-size: 1.2rem;
}
</style>
