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
                        <Chart :min="min" :max="max" :step="step" :f1="f1Shifted" :input="Number(input)" />
                    </div>
                    <div>
                        <Chart :min="min" :max="max" :step="step" :f1="f2Shifted" :input="Number(input)" />
                    </div>
                </section>
                <section class="flex justify-between">
                    <input v-model.number="shift1" type="number">
                    <input v-model.number="shift2" type="number">
                </section>
                <section>
                        <CombinedChart :min="min" :max="max" :step="step" :f1="f1Shifted" :f2="f2Shifted" :input="Number(input)" />
                </section>
    </main>
</template>

<script setup>
import { ref, computed, watch } from 'vue';
import Chart from './components/Chart.vue';
import CombinedChart from './components/CombinedChart.vue';
import * as math from 'mathjs';

const input = ref(10);
const min = ref(0);
const max = ref(15);
const step = 0.5;
const shift1 = ref(2);
const shift2 = ref(0);

const f1 = ref("x + 3 + x");
const f2 = ref("-x + 15");

function shiftFunction(inputFunction, shiftValue) {
  const parsedFunction = math.parse(inputFunction);

  const shiftedNode = parsedFunction.transform(function (node, path, parent) {
    if (node.isSymbolNode && node.name === 'x') {
      return math.parse(`x + ${shiftValue}`);
    }
    return node;
  });

  const simplifiedFunction = math.simplify(shiftedNode);
  return simplifiedFunction.toString();
}

const f1Shifted = computed(() => shiftFunction(f1.value, parseFloat(shift1.value)));
const f2Shifted = computed(() => shiftFunction(f2.value, parseFloat(shift2.value)));

watch([shift1, shift2, f1, f2], ([newShift1, newShift2, newF1, newF2]) => {
  f1Shifted.value = shiftFunction(newF1, parseFloat(newShift1));
  f2Shifted.value = shiftFunction(newF2, parseFloat(newShift2));
});
</script>
<style scoped>
input {
    color: black
}
</style>
