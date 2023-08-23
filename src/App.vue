<template>
    <header>
        <div class="wrapper">
            <Title msg="Price Quantity Function!" />
        </div>
    </header>

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
        </div>
        <div class="flex gap-8 justify-between">
            <div class="flex flex-col w-full">
                <label for="slider">value: </label>
                <input v-model="input" id="slider" type="range" :min="min" :max="max">
            </div>
            <div class="flex flex-col">
                <label for="value">value: </label>
                <input v-model="input" id="value" type="number">
            </div>
        </div>
    </main>
</template>

<script setup>
    import { ref, watch } from 'vue';
    import Title from './components/Title.vue'
    import * as math from 'mathjs';

    const input = ref(10);
    const min = ref(0);
    const max = ref(15);

    const f1 = ref("1/2 x - 2");
    const f2 = ref("-1/4 x + 5");

    /*watch([input, min, max], () => {
        console.log(math.chain(input.value).multiply(5).add(7));
    })*/

    watch(input, () => {
        console.log(math.evaluate(f1.value, {x: input.value}));
        console.log(math.evaluate(f2.value, {x: input.value}));
    })
</script>

<style scoped>
input {
    color: #000;
}

header {
    line-height: 1.5;
}

.logo {
    display: block;
    margin: 0 auto 2rem;
}

              @media (min-width: 1024px) {
                  header {
                      display: flex;
                      place-items: center;
                      padding-right: calc(var(--section-gap) / 2);
                  }

                  .logo {
                      margin: 0 2rem 0 0;
                  }

                  header .wrapper {
                      display: flex;
                      place-items: flex-start;
                      flex-wrap: wrap;
                  }
              }
</style>
