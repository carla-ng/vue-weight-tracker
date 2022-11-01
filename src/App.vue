<script setup>
  import { ref, shallowRef, computed, watch, nextTick } from 'vue'
  import Chart from 'chart.js/auto'

  const weights = ref([])
  const weightChartEl = ref(null)
  const weightChart = shallowRef(null)
  const weightInput = ref(51.0)

  const currentWeight = computed(() => {
    return weights.value.sort((a, b) => b.date - a.date)[0] || { weight: 0 }
  })

  const addWeight = () => {
    weights.value.push({
      weight: weightInput.value,
      date: new Date().getTime()
    })
  }

  watch( weights, newWeights => {
    const ws = [...newWeights]

    if ( weightChart.value ) {
      weightChart.value.data.labels = ws
                                      .sort( (a, b) => a.date - b.date )
                                      .map( w => new Date(w.date).toLocaleDateString() )
                                      .slice(-7)

      weightChart.value.data.datasets[0].data = ws
                                                .sort( (a, b) => a.date - b.date )
                                                .map( w => w.weight )
                                                .slice(-7)

      weightChart.value.update()

      return
    }

    nextTick(() => {
      weightChart.value = new Chart( weightChartEl.value.getContext('2d'), {
        type: 'line',
        data: {
          labels: ws
                  .sort( (a, b) => a.date - b.date )
                  .map( w => new Date(w.date).toLocaleDateString() ),
          datasets: [
            {
              label: 'Weight',
              data: ws
                    .sort( (a, b) => a.date - b.date )
                    .map( w => w.weight ),
              backgroundColor: 'rgba(255, 105, 180, 0.2)',
              borderColor: 'rgb(255, 105, 180)',
              borderWidth: 1,
              fill: true
            }
          ]
        },
        options: {
              responsive: true,
              maintainAspectRatio: false
        }
      })
    })
  })

</script>

<template>
  <main>

    <h1>Weight Tracker</h1>

    <div class="current">
      <small>Current weight (kg):</small>
    </div>

    <form @submit.prevent="addWeight">
      <input type="number" step="0.1" v-model="weightInput" />
      <input type="submit" value="Add weight" />
    </form>

    <div v-if="weights && weights.length > 0">
      <h3>Last 7 days</h3>

      <div class="canvas-box">
        <canvas ref="weightChartEl"></canvas>
      </div>

      <div class="weight-history">
        <h3>Weight history</h3>

        <ul>
          <li v-for="weight in weights" :key="weight.weight">
            <span>{{ weight.weight }} kg </span>
            <small> - {{ new Date(weight.date).toLocaleDateString() }}</small>
          </li>
        </ul>
      </div>
    </div>

  </main>
</template>

<style scoped>

</style>
