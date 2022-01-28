<template lang="pug">
.type-chart
  canvas#typeChart
</template>

<script setup lang="ts">
import { typeColors } from '../../util/ColorMapper';
import { Chart, registerables } from "chart.js";
import { watch } from "vue";

Chart.register(...registerables)

interface TypeChartProps {
  types: Map<string, number>
}

const props = defineProps<TypeChartProps>()

watch(() => props.types, () => {
  const ctx = document.getElementById('typeChart')!.getContext('2d')
  new Chart(ctx, {
    type: 'pie',
    data: {
      labels: [...props.types.keys()],
      datasets: [{
        data: [...props.types.values()],
        backgroundColor: [...props.types.keys()].map(type => typeColors[type]),
      }]
    },
    options: {
      plugins: {
        legend: {
          position: 'right'
        }
      }
    }
  })
})
</script>

<style lang="stylus" scoped>
.type-chart
  width 600px
</style>