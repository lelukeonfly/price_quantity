<template>
  <div class="chart-container">
    <canvas ref="chartCanvas"></canvas>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, toRefs } from 'vue';
import * as math from 'mathjs';
import { Chart as ChartJS, registerables } from 'chart.js';

const props = defineProps({
  min: Number,
  max: Number,
  step: Number,
  f1: String,
  f2: String,
  input: Number
});

const { min, max, step, f1, f2, input } = toRefs(props);

ChartJS.register(...registerables);

const chartCanvas = ref(null);

const options = {
  // ...your options here...
};

let xValues1 = [];
let yValues1 = [];
let yValues2 = [];

const data = {
  labels: xValues1,
  datasets: [
    {
      label: 'Function 1',
      fill: false,
      pointRadius: 1,
      borderColor: 'rgba(255, 0, 0, 0.5)',
      data: yValues1
    },
    {
      label: 'Function 2',
      fill: false,
      pointRadius: 1,
      borderColor: 'rgba(0, 0, 255, 0.5)',
      data: yValues2
    },
    {
      label: 'Point',
      data: [],
      backgroundColor: 'red',
      pointRadius: 5,
      pointStyle: 'circle'
    }
  ]
};

function generateData(c, f, yValues, min, max, step = 1) {
  xValues1.length = 0;
  yValues.length = 0;

  for (let i = min; i <= max; i += step) {
    let y;
    try {
      y = math.evaluate(f, { x: i });
    } catch (e) {
      console.error(e);
    }
    yValues.push(y);
    xValues1.push(i);
  }

  c.data.labels = xValues1;
  c.data.datasets[0].data = yValues1;
  c.data.datasets[1].data = yValues2;
  c.update();
}

function generatePoint(c, fin, x, yValues) {
  const yValue = math.evaluate(fin, { x: parseFloat(x) });
  c.data.datasets[2].data = [{ x: parseFloat(x), y: yValue }];
  c.update();
  return yValue;
}

let chart;

onMounted(async () => {
  chart = createChart(chartCanvas.value, data, options);
  generateData(chart, f1.value, yValues1, min.value, max.value, step.value);
  generateData(chart, f2.value, yValues2, min.value, max.value, step.value);
  a = generatePoint(chart, f1.value, input.value, yValues1);
  b = generatePoint(chart, f2.value, input.value, yValues2);
});

watch([min, max, step, f1, f2], () => {
  generateData(chart, f1.value, yValues1, min.value, max.value, step.value);
  generateData(chart, f2.value, yValues2, min.value, max.value, step.value);
  a = generatePoint(chart, f1.value, input.value, yValues1);
  b = generatePoint(chart, f2.value, input.value, yValues2);
});

watch(input, () => {
  a = generatePoint(chart, f1.value, input.value, yValues1);
  b = generatePoint(chart, f2.value, input.value, yValues2);
});
</script>

<style scoped>
.chart-container {
  max-width: 800px;
  margin: 0 auto;
}
</style>

