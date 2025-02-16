<script setup>
import { computed, onMounted, ref } from 'vue'
import CountryData from './components/CountryData.vue'
import GoogleChart from './components/GoogleChart.vue'

const name = ref('')
const selectedCodes = ref([])
const countryNames = ref([])
const countryData = ref({})
const selectedCountry = ref([])
const selectedDataType = ref('population')

const dataTypes = ['population', 'pib', 'area', 'income']

const fetchCountryData = async (countryCode) => {
  try {
    const res = await fetch('http://localhost:3000/api/country/' + countryCode)
    if (!res.ok) {
      throw new Error("País no encontrado")
    }
    const data = await res.json();
    console.log(data)
    return data
  } catch (error) {
    console.log('Error al recibir los datos', error);
  }
}

const deleteCountryData = async (countryCode) => {
  try {
    const res = await fetch('http://localhost:3000/api/country/' + countryCode, {
      method: 'DELETE'
    })
    if (!res.ok) {
      throw new Error("Error al eliminar los datos")
    }
    console.log('Eliminando:' + countryCode)
    unmark(countryCode)
  } catch (error) {
    console.log('Error al eliminar los datos', error)
  }
}

const selectCode = async (countryName) => {
  const countryCode = Object.keys(countryData.value).find((code) => countryData.value[code] === countryName)
  if (!countryCode) {
    console.log('Código no encontrado')
    return
  }
  const data = await fetchCountryData(countryCode)
  if (data && !selectedCodes.value.includes(countryCode)) {
    selectedCodes.value.push(countryCode)
    selectedCountry.value = data
    console.log(selectedCodes.value)
  }
}

const unmark = (code) => {
  selectedCodes.value = selectedCodes.value.filter((c) => c !== code)
  if (selectedCountry.value && selectedCountry.value.code == code) {
    selectedCountry.value = null
  }
}

const sortedSelectedCodes = computed(() => {
  return [...selectedCodes.value].sort((a, b) => a.localeCompare(b))
})

const selectedCountryNames = computed(() => {
  return sortedSelectedCodes.value.map((code) => countryData.value[code])
})

const chartData = computed(() => {
  if (!selectedCountry.value) {
    return []
  }

const data = [
  ['País', selectedDataType.value],
  [selectedCountry.value.name, selectedCountry.value[selectedDataType.value]]
]
return data
})

onMounted(async () => {
  try {
    const res = await fetch('http://localhost:3000/api/names')
    const data = await res.json()
    countryData.value = data
    countryNames.value = Object.values(data)
  } catch (error) {
    console.log('Error al recibir los datos', error)
  }
})
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
        <li v-for="(countryName, index) in selectedCountryNames" :key="code">
          {{ countryName }}
          <button @click="unmark(sortedSelectedCodes[index])">Desmarcar</button>
        </li>
      </ul>
    </div>
    <div class="country-data">
      <CountryData :country="selectedCountry" @delete-country="deleteCountryData"></CountryData>
    </div>
    <div class="options">
      <label v-for="(type, index) in dataTypes" :key="index">
        <input type="radio" :id="type" :name="type" :value="type" v-model="selectedDataType">
        {{ type }}
      </label>
    </div>
    <div class="chart">
      <GoogleChart :data="chartData" />
    </div>
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
