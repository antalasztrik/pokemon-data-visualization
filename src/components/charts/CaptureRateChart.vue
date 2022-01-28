<template lang="pug">
.capture-rate-chart
  canvas#captureRateChart
</template>

<script setup lang="ts">
import { watch } from "vue";
import {Chart} from "chart.js";

interface CaptureRateChartProps {
  captureRates: Map<number, number>
}

const props = defineProps<CaptureRateChartProps>()

watch(() => props.captureRates, () => {
  const ctx = document.getElementById('captureRateChart')!.getContext('2d')
  new Chart(ctx, {
    type: 'bar',
    data: {
      labels: [...props.captureRates.keys()],
      datasets: [{
        label: 'Pokemon capture rates',
        data: [...props.captureRates.values()],
        backgroundColor: [...new Array(props.captureRates.size)].map((a, i) => `rgb(${100 + i * 5}, 0, 0)`),
      }]
    }
  })
})
</script>

<style lang="stylus" scoped>
.capture-rate-chart
  width 1000px
</style>