<template>
    <main class="flex flex-col gap-3">
        <div class="flex gap-3 justify-between">
            <div class="flex flex-col">
                <label for="min">min: </label>
                <input v-model="min" id="min" type="number">
            </div>
            <div class="flex flex-col">
                <label for="max">max: </label>
                <input v-model="max" id="max" type="number">
            </div>
            <div class="flex flex-col">
                <label for="step">step: </label>
                <input v-model="step" id="step" type="number">
            </div>
        </div>
        <div class="flex gap-8 justify-between">
            <div class="flex flex-col w-full">
                <label for="slider">value: </label>
                <input v-model="input" id="slider" type="range" :min="min" :max="max" :step="step">
            </div>
            <div class="flex flex-col">
                <label for="value">value: </label>
                <input v-model="input" id="value" type="number">
            </div>
        </div>
        <div class="flex gap-3 justify-between">
            <div class="flex flex-col">
                <label for="f1">f1(x): </label>
                <input v-model="f1" id="f1" type="text">
            </div>
            <div class="flex flex-col">
                <label for="f2">f2(x): </label>
                <input v-model="f2" id="f2" type="text">
            </div>
        </div>
        <canvas id="fone"></canvas>
        <canvas id="ftwo"></canvas>
    </main>
</template>

<script setup>
    import { ref, watch, onMounted, nextTick } from 'vue';
    import Title from './components/Title.vue'
    import * as math from 'mathjs';
    import { Chart as ChartJS, LineElement, LinearScale, registerables } from 'chart.js';

    ChartJS.register(...registerables);

    const input = ref(10);
    const min = ref(0);
    const max = ref(15);
    const step = ref(0.5);
    const f1 = ref("1/2 x - 2");
    const f2 = ref("-1/4 x + 5");

    let xValues = [];
    let yValues = [];

    // show function with sample points
    function generateData(f, min, max, step = 1) {
        xValues = [];
        yValues = [];
        for (let i = min; i <= max; i += step) {
            let y;
            try{
                y = math.evaluate(f, {x: i})
            }catch(e){
                console.error(e)
            }
            yValues.push(y);
            xValues.push(i);
        }
        chart.data.labels = xValues;
        chart.data.datasets[0].data = yValues;
        chart.update();
        //console.log(xValues)
        //console.log(yValues)
    }

    // calc point
    function generatePoint(f, x){
        const yValue = math.evaluate(f, {x: parseFloat(x)});
        chart.data.datasets[1].data = [{x: parseFloat(x), y: yValue}];
        chart.update()
        //console.log(chart.data.datasets[1].data)
    }

    let chart = [];

    // load chart when site loaded
    onMounted(async () => {

        chart = new ChartJS("fone", {
            type: "line",
            data: {
                labels: xValues,
                datasets: [{
                    label: 'f1',
                    fill: false,
                    pointRadius: 1,
                    borderColor: "rgba(255,0,0,0.5)",
                    data: yValues,
                    },{
                      label: 'Point',
                      data: [],
                      backgroundColor: 'red', // Customize the color of the point
                      pointRadius: 5, // Customize the size of the point
                      pointStyle: 'circle' // Customize the shape of the point
                }
                ],
            },
            options: {
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
                    text: "y = x * 2 + 7",
                    fontSize: 16,
                },
            },
        });

        // generate first view with default
        generateData(f1.value, min.value, max.value, step.value);
        generatePoint(f1.value, input.value)

    });


    // update chart
    watch([min, max, step, f1], () => {
        generateData(f1.value, min.value, max.value, step.value);
        chart.update();
    })

    // update point
    watch(input, () => {
        generatePoint(f1.value, input.value)
    })

</script>

<style scoped>

input {
    color: black;
}

* {
    color: white;
}

</style>
