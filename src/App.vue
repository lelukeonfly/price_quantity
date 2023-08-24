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
                <section class="grid grid-cols-2">
                    <div>
                        <Chart :min="min" :max="max" :step="step" :f1="f1" :input="Number(input)" />
                    </div>
                    <div>
                        <Chart :min="min" :max="max" :step="step" :f1="f2" :input="Number(input)" />
                    </div>
                </section>
    </main>
</template>

<script setup>
    import { ref /*, watch, onMounted */} from 'vue';
//import * as math from 'mathjs';
import { Chart as ChartJS, registerables } from 'chart.js';
import Chart from './components/Chart.vue';

ChartJS.register(...registerables);

const input = ref(10);
const min = ref(0);
const max = ref(15);
//const step = ref(0.5);
const step = .5;
const f1 = ref("1/2 x - 2");
const f2 = ref("-1/4 x + 5");
</script>
