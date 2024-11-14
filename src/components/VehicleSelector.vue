<template>
  <div class="vehicle-selector">
    <div class="selector-group">
      <label for="year">Year:</label>
      <select id="year" v-model="selectedYear" @change="handleYearChange">
        <option value="">Select Year</option>
        <option v-for="year in years" :key="year" :value="year">
          {{ year }}
        </option>
      </select>
    </div>

    <div class="selector-group">
      <label for="make">Make:</label>
      <select id="make" v-model="selectedMake" @change="handleMakeChange" :disabled="!selectedYear">
        <option value="">Select Make</option>
        <option v-for="make in makes" :key="make" :value="make">
          {{ make }}
        </option>
      </select>
    </div>

    <div class="selector-group">
      <label for="model">Model:</label>
      <select id="model" v-model="selectedModel" :disabled="!selectedMake">
        <option value="">Select Model</option>
        <option v-for="model in models" :key="model" :value="model">
          {{ model }}
        </option>
      </select>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const API_TOKEN = '5cbe12fb62f4941267d623499a2a4fd5948fd3ef'
const BASE_URL = 'https://rateengine.ship.cars/v2/vehicles'

const years = ref([])
const makes = ref([])
const models = ref([])

const selectedYear = ref('')
const selectedMake = ref('')
const selectedModel = ref('')

const headers = {
  'Accept': 'application/json',
  'Content-Type': 'application/json',
  'Access-Control-Allow-Origin': '*'
}

async function fetchYears() {
  try {
    const response = await axios.get(`${BASE_URL}/years/?token=${API_TOKEN}`, { headers })
    years.value = response.data
  } catch (error) {
    console.error('Error fetching years:', error)
  }
}

async function fetchMakes(year) {
  try {
    const response = await axios.get(
      `${BASE_URL}/makes/?year=${year}&token=${API_TOKEN}`,
      { headers }
    )
    makes.value = response.data
  } catch (error) {
    console.error('Error fetching makes:', error)
  }
}

async function fetchModels(year, make) {
  try {
    const response = await axios.get(
      `${BASE_URL}/models/?year=${year}&make=${make}&token=${API_TOKEN}`,
      { headers }
    )
    models.value = response.data
  } catch (error) {
    console.error('Error fetching models:', error)
  }
}

function handleYearChange() {
  selectedMake.value = ''
  selectedModel.value = ''
  models.value = []
  if (selectedYear.value) {
    fetchMakes(selectedYear.value)
  } else {
    makes.value = []
  }
}

function handleMakeChange() {
  selectedModel.value = ''
  if (selectedYear.value && selectedMake.value) {
    fetchModels(selectedYear.value, selectedMake.value)
  } else {
    models.value = []
  }
}

onMounted(() => {
  fetchYears()
})
</script>

<style scoped>
.vehicle-selector {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  max-width: 300px;
  margin: 0 auto;
}

.selector-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

select {
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
}

select:disabled {
  background-color: #f5f5f5;
  cursor: not-allowed;
}

label {
  font-weight: bold;
}
</style>
