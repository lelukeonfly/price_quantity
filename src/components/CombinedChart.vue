<template>
    <canvas ref="chartCanvas"></canvas>
    <div>
        {{ a }}
    </div>
    <div>
        {{ b }}
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

function createChart(canvas, data, options) {
  const ctx = canvas.getContext('2d');
  return new ChartJS(ctx, {
    type: 'line',
    data,
    options,
  });
}

const options = {
    scales: {
      y: {
        beginAtZero: true,
        min: 0,
        grid: {
          borderColor: 'red', // Set the grid border color
          color: 'white', // Set the grid line color
          borderWidth: 1, // Set the grid border width
        },
      },
      x: {
        beginAtZero: true,
        min: 0,
        grid: {
          borderColor: 'red', // Set the grid border color
          color: 'white', // Set the grid line color
          borderWidth: 1, // Set the grid border width
        },
      },
    },
    legend: { display: false },
    title: {
      display: true,
      fontSize: 16,
    },
  }

let xValues = [];
let yValues1 = [];
let yValues2 = [];

const data = {
  labels: xValues,
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

function generateData(c, f1, f2, min, max, step = 1) {
  xValues.length = 0;
  yValues1.length = 0;
  yValues2.length = 0;

  for (let i = min; i <= max; i += step) {
    let y1;
    let y2;
    try {
      y1 = math.evaluate(f1, { x: i });
      y2 = math.evaluate(f2, { x: i });
    } catch (e) {
      console.error(e);
    }
    xValues.push(i);
    yValues1.push(y1);
    yValues2.push(y2);
  }

  c.data.labels = xValues;
  c.data.datasets[0].data = yValues1;
  c.data.datasets[1].data = yValues2;
  c.update();
}

function generatePoint(c, fin, x) {
  const yValue = math.evaluate(fin, { x: parseFloat(x) });
  //c.data.datasets[2].data = [{ x: parseFloat(x), y: yValue }];
  c.update();
  return [x, yValue];
}

let chart;
let a;
let b;

onMounted(async () => {
  chart = createChart(chartCanvas.value, data, options);
  generateData(chart, f1.value, f2.value, min.value, max.value, step.value);
  //generateData(chart, f2.value, yValues2, min.value, max.value, step.value);
  a = generatePoint(chart, f1.value, input.value, yValues1);
  //b = generatePoint(chart, f2.value, input.value, yValues2);
});

watch([min, max, step, f1, f2], () => {
  //generateData(chart, f1.value, yValues1, min.value, max.value, step.value);
  generateData(chart, f1.value, f2.value, min.value, max.value, step.value);
  a = generatePoint(chart, f1.value, input.value, yValues1);
  //b = generatePoint(chart, f2.value, input.value, yValues2);
});

watch(input, () => {
  a = generatePoint(chart, f1.value, input.value);
  chart.data.datasets[2].data = [{ x: a[0], y: a[1]}];
  b = generatePoint(chart, f2.value, input.value);
  chart.data.datasets[3].data = [{ x: b[0], y: b[1]}];
  chart.update()
});
</script>

<style scoped>
.chart-container {
  max-width: 800px;
  margin: 0 auto;
}
</style>

