<script setup>
  import { ref, shallowRef, computed, watch, nextTick } from 'vue'
  import Chart from 'chart.js/auto'

  const weights = []
  const weightChartEl = ref(null)
  const weightChart = shallowRef(null)
  const weightInput = ref(51.0)

  const currentWeight = computed(() => {
    return weights.values.sort((a,b) => b.date - a.date)[0] || { weight: 0 }
  })

  const addWeight = () => {
    weights.values.push({
      weight: weightInput.value,
      date: new Date().getTime
    })
  }
  
</script>

<template>
  <h1>Hello, world</h1>
</template>

<style scoped>

</style>
