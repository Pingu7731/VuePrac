<script setup>
import { ref, shallowRef, computed, watch, nextTick } from 'vue'
import Chart from 'chart.js/auto'

const weights = ref([])
const weightChartEl = ref(null)
const weightChart = shallowRef(null)
const weightInput = ref(0.0)
const currentWeight = computed(() => {
  return weights.value.sort((a, b) => b.date - a.date)[0] || { weight: 0 }
})
const addWeight = () => {
  weights.value.push({
    weight: weightInput.value,
    date: new Date().getTime()
  })
}
watch(weights, newWeights => {
  const ws = [...newWeights];

  nextTick(() => {
    weight.chart.value = new Chart(weightChartEl.value.getcContext('2d'), {
      type: 'line',
      data: {
        label: ws.sort((a, b) => a.date - b.date).map(w => new Date(w.date).toLocaleDateString),
        datasets: [
          {
            label: 'Weight',
            data: ws.sort((a, b) => a.date - b.date).map(w => w.weight),
            backgroundColor: 'rgba(255,105,180,0.2)',
            borderWidth: 1,
            fill: true,
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRation: false
      }
    })
  })

}, { deep: true })
</script>

<template>
  <main>
    <h1>Weight Tracker</h1>
    <div class="current">
      <span>{{ currentWeight.weight }}</span>
      <small> Current Weight (kg)</small>
    </div>

    <form @submit.prevent="addWeight">
      <input type="number" step="0.1" v-model="weightInput">
      <input type="submit" value="Add">
    </form>

    <div v-if="weights && weights.length > 2">
      <h2>Last 7 days</h2>
      <div class="canvas-box">
        <canvas ref="weightChartEl"></canvas>
      </div>
      <div class="weight-history">
        <h2>History</h2>
        <ul>
          <li v-for="weight in weights">
            <span>{{ weight.weight }} kg </span>
            <small>{{ new Date(weight.date).toLocaleDateString() }}</small>
          </li>
        </ul>
      </div>
    </div>

  </main>
</template>

<style scoped></style>
