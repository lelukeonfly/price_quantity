<template>
    <div class="flex gap-3 justify-between">
      <div class="flex flex-col">
        <label for="f1">f1(x): </label>
        <input v-model="f1" id="f1" type="text" class="text-black rounded-lg p-2">
      </div>
    </div>
    <canvas ref="chartCanvas"></canvas>
    <div class="text-white">{{ a }}</div>
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
    input: Number
})

const { min, max, step, f1, input} = toRefs(props)

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


//const f1 = ref("1/2 x - 2");

let a = 0;

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

let xValues1 = [];
let yValues1 = [];

const data1 = {
  labels: xValues1,
  datasets: [
    {
      label: 'function',
      fill: false,
      pointRadius: 1,
      borderColor: "rgba(255,0,0,0.5)",
      data: yValues1,
    },
    {
      label: 'Point',
      data: [],
      backgroundColor: 'red',
      pointRadius: 5,
      pointStyle: 'circle',
    },
  ],
};

function generateData(c, f, min, max, step = 1) {

  xValues1.length = 0;
  yValues1.length = 0;

  for (let i = min; i <= max; i += step) {
    let y;
    try {
      y = math.evaluate(f, { x: i });
    } catch (e) {
      //console.error(e);
    }
    yValues1.push(y);
    xValues1.push(i);
  }

  c.data.labels = xValues1;
  c.data.datasets[0].data = yValues1;
  c.update();
}
function generatePoint(c, fin, x) {
  const yValue = math.evaluate(fin, { x: parseFloat(x) });
  //console.log(yValue1);
  c.data.datasets[1].data = [{ x: parseFloat(x), y: yValue }];
  c.update();
  return yValue
}

let chart;

onMounted(async () => {
  chart = createChart(chartCanvas.value, data1, options);
  generateData(chart, f1.value, min.value, max.value, step.value);
  a = generatePoint(chart, f1.value, input.value);
});

watch([min, max, step, f1], () => {
  generateData(chart, f1.value, min.value, max.value, step.value);
  a = generatePoint(chart, f1.value, input.value);
});

watch(input, () => {
  a = generatePoint(chart, f1.value, input.value);
});
</script>
