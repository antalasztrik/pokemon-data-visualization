<template lang="pug">
.legendary-chart
  canvas#legendaryChart
</template>

<script setup lang="ts">
import { computed, watch } from "vue"
import type { ComputedRef } from "vue"
import type { PokemonType } from '../../util/PokemonType'
import { Chart } from "chart.js"
import { typeColors } from '../../util/ColorMapper';

interface LegendaryChartProps {
  legendaries: Array<PokemonType>
}

const props = defineProps<LegendaryChartProps>()

let dragonLegendaries: ComputedRef<Array<PokemonType>>
const dragonLegendariesCount = computed(() => dragonLegendaries.value.length)
const nonDragonLegendariesCount = computed(() => props.legendaries.length - dragonLegendariesCount.value)
watch(() => props.legendaries, () => {
  dragonLegendaries = computed(() => {
    return props.legendaries.filter(legendary => legendary.type1 === 'dragon' || legendary.type2 === 'dragon')
  })

  const ctx = document.getElementById('legendaryChart')!.getContext('2d')
  new Chart(ctx, {
    type: 'bar',
    data: {
      labels: ['Non dragon legendaries', 'Dragon legendaries'],
      datasets: [{
        label: 'Legendaries',
        data: [nonDragonLegendariesCount.value, dragonLegendariesCount.value],
        backgroundColor: [typeColors['dragon'], typeColors['normal']],
      }]
    }
  })
})
</script>

<style lang="stylus" scoped>
.legendary-chart
  width 300px
</style>