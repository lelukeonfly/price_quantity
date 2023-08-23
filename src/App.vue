<template>
  <main class="flex flex-col gap-3">
    <div class="flex gap-3 justify-between">
      <div class="flex flex-col">
        <label for="min">min: </label>
        <input v-model="min" id="min" type="number" class="text-black rounded-lg p-2">
      </div>
      <div class="flex flex-col">
        <label for="max">max: </label>
        <input v-model="max" id="max" type="number" class="text-black rounded-lg p-2">
      </div>
      <!--div class="flex flex-col">
        <label for="step">step: </label>
        <input v-model="step" id="step" type="number" class="text-black rounded-lg p-2">
      </div-->
    </div>
    <div class="flex gap-8 justify-between">
      <div class="flex flex-col w-full">
        <label for="slider">value: </label>
        <input v-model="input" id="slider" type="range" :min="min" :max="max" :step="step" class="text-black rounded-lg p-2">
      </div>
      <div class="flex flex-col">
        <label for="value">value: </label>
        <input v-model="input" id="value" type="number" class="text-black rounded-lg p-2">
      </div>
    </div>
    <div class="flex gap-3 justify-between">
      <div class="flex flex-col">
        <label for="f1">f1(x): </label>
        <input v-model="f1" id="f1" type="text" class="text-black rounded-lg p-2">
      </div>
      <div class="flex flex-col">
        <label for="f2">f2(x): </label>
        <input v-model="f2" id="f2" type="text" class="text-black rounded-lg p-2">
      </div>
    </div>
    <canvas ref="chartCanvas"></canvas>
    <div class="text-white">{{ a }}</div>
    <canvas ref="chart2Canvas"></canvas>
    <div class="text-white">{{ b }}</div>
  </main>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';
import * as math from 'mathjs';
import { Chart as ChartJS, registerables } from 'chart.js';

ChartJS.register(...registerables);

const chartCanvas = ref(null);
const chart2Canvas = ref(null);

function createChart(canvas, data, options) {
  const ctx = canvas.getContext('2d');
  return new ChartJS(ctx, {
    type: 'line',
    data,
    options,
  });
}

const input = ref(10);
const min = ref(0);
const max = ref(15);
//const step = ref(0.5);
const step = .5;
const f1 = ref("1/2 x - 2");
const f2 = ref("-1/4 x + 5");

let a = 0;
let b = 0;

/*function getStep(){
    if(step <= 0){
        return 1
    }
    return step.value
}*/

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
let xValues2 = [];
let yValues2 = [];

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

const data2 = {
  labels: xValues2,
  datasets: [
    {
      label: 'function',
      fill: false,
      pointRadius: 1,
      borderColor: "rgba(255,0,0,0.5)",
      data: yValues2,
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
  const xValues = c === chart ? xValues1 : xValues2;
  const yValues = c === chart ? yValues1 : yValues2;

  xValues.length = 0;
  yValues.length = 0;

  for (let i = min; i <= max; i += step) {
    let y;
    try {
      y = math.evaluate(f, { x: i });
    } catch (e) {
      console.error(e);
    }
    yValues.push(y);
    xValues.push(i);
  }

  c.data.labels = xValues;
  c.data.datasets[0].data = yValues;
  c.update();
}
function generatePoint(c, fin, x) {
  const yValue = math.evaluate(fin, { x: parseFloat(x) });
  //console.log(yValue);
  c.data.datasets[1].data = [{ x: parseFloat(x), y: yValue }];
  c.update();
  return yValue
}

let chart;
let chart2;

onMounted(async () => {
  chart = createChart(chartCanvas.value, data1, options);
  chart2 = createChart(chart2Canvas.value, data2, options);
  generateData(chart, f1.value, min.value, max.value, step);
  generateData(chart2, f2.value, min.value, max.value, step);
  a = generatePoint(chart, f1.value, input.value);
  b = generatePoint(chart2, f2.value, input.value);
});

watch([min, max, /*step, */f1, f2], () => {
  generateData(chart, f1.value, min.value, max.value, step);
  generateData(chart2, f2.value, min.value, max.value, step);
  a = generatePoint(chart, f1.value, input.value);
  b = generatePoint(chart2, f2.value, input.value);
});

watch(input, () => {
  a = generatePoint(chart, f1.value, input.value);
  b = generatePoint(chart2, f2.value, input.value);
});
</script>
