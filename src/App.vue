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

  watch( weights, (newWeights) => {
    const ws = [...newWeights]

    if ( weightChart.value ) {
      weightChart.value.data.labels = ws
                                      .sort( (a, b) => a.date - b.date )
                                      .map( weight => new Date(weight.date).toLocaleDateString('es-ES') )
                                      .slice(-7)

      weightChart.value.data.datasets[0].data = ws
                                                .sort( (a, b) => a.date - b.date )
                                                .map( weight => weight.weight )
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
                  .map( weight => new Date(weight.date).toLocaleDateString('es-ES') ),
          datasets: [
            {
              label: 'Weight',
              data: ws
                    .sort( (a, b) => a.date - b.date )
                    .map( weight => weight.weight ),
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
  }, { deep: true })

</script>


<template>
  <main>

    <h1>Weight Tracker</h1>

    <div class="current">
      <span>{{ currentWeight.weight }}</span>
      <small>Current weight (kg)</small>
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
            <small>{{ new Date(weight.date).toLocaleDateString('es-ES') }}</small>
          </li>
        </ul>
      </div>
    </div>

  </main>
</template>


<style scoped>
* {
	box-sizing: border-box;
	font-family: 'montserrat', sans-serif;
	margin: 0;
	padding: 0;
}
body {
	background-color: #eee;
}
main {
	padding: 1.5rem;
}
h1 {
	font-size: 2em;
	margin-bottom: 2rem;
	text-align: center;
}
h2 {
	margin-bottom: 1rem;
	color: #888;
	font-weight: 400;
}
.current {
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	height: 200px;
	width: 200px;
	
	background-color: white;
	border-radius: 999px;
	border: 5px solid hwb(330 41% 0%);
	box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin: 0 auto 2rem;
	text-align: center;
}
.current span {
	display: block;
	font-size: 2em;
	font-weight: bold;
	margin-bottom: 0.5rem;
}
.current small {
	color: #888;
	font-style: italic;
}
form {
	display: flex;
	border-radius: 0.5rem;
	border: 1px solid #AAA;
	margin-bottom: 2rem;
	overflow: hidden;
	transition: 200ms linear;
}
form:focus-within,
form:hover {
	border-color: hotpink;
	border-width: 2px;
}
form input[type="number"] {
	appearance: none;
	border: none;
	outline: none;
	background-color: white;
	flex: 1 1 0%;
	font-size: 1.25rem;
	padding: 1rem 1.5rem;
}
form input[type="submit"] {
	appearance: none;
	border: none;
	outline: none;
	background-color: hotpink;
	border-left: 3px solid transparent;
	color: white;
	cursor: pointer;
	font-size: 1.25rem;
	font-weight: 700;
	padding: 0.5rem 1rem;
	transition: 200ms linear;
}
form input[type="submit"]:hover {
	background-color: white;
	border-left-color: hotpink;
	color: hotpink;
}
.canvas-box {
	background-color: white;
	border-radius: 0.5rem;
	box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
	margin-bottom: 2rem;
	max-width: 720px;
	padding: 1rem;
	width: 100%;
}
.weight-history ul {
	list-style: none;
	padding: 0;
	margin: 0;
}
.weight-history ul li {
	display: flex;
	align-items: center;
	justify-content: space-between;
	cursor: pointer;
	padding: 0.5rem;
}
.weight-history ul li:nth-child(odd) {
	background-color: #dfdfdf;
}
.weight-history ul li:hover {
	background-color: #f8f8f8;
}
.weight-history ul li:last-of-type {
	border-bottom: none;
}
.weight-history ul li span {
	display: block;
	font-size: 1.25rem;
	font-weight: 700;
	margin-right: 1rem;
}
.weight-history ul li small {
	color: #888;
	font-style: italic;
}
</style>
