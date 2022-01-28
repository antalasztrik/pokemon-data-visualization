<template lang="pug">
.height-chart
  canvas#heightChart
</template>

<script setup lang="ts">
import { computed, watch } from "vue";
import { Chart } from "chart.js";

interface HeightChartProps {
  heights: Array<{ name: string, height: number }>
}
const props = defineProps<HeightChartProps>()

const names = computed(() => props.heights.map(pokemon => pokemon.name))
const heights = computed(() => props.heights.map(pokemon => pokemon.height))

watch(() => props.heights, () => {
  const ctx = document.getElementById('heightChart')!.getContext('2d')
  new Chart(ctx, {
    type: 'bar',
    data: {
      labels: names.value,
      datasets: [{
        label: 'Largest Pokemons by height (meter)',
        data: heights.value,
        backgroundColor: 'rgb(20, 220, 20)',
      }]
    },
    options: {
      scales: {
        y: {
          beginAtZero: true
        }
      }
    },
  })
})
</script>

<style lang="stylus" scoped>
.height-chart
  width 600px
</style>