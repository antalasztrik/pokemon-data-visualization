<template lang="pug">
main.main
  h4 We have data from {{ pokemons.length }} Pokemons from {{ generations }} generations!
  .data
    TypeChart(:types="types")
    HeightChart(:heights="heights")
    CaptureRateChart(:capture-rates="captureRates")
    LegendaryChart(:legendaries="legendaries")
</template>

<script setup lang="ts">
import parser from 'papaparse'
import { computed, ref } from 'vue';
import type { Ref } from 'vue'
import type { PokemonType } from '../util/PokemonType';
import TypeChart from './charts/TypeChart.vue';
import HeightChart from './charts/HeightChart.vue'
import CaptureRateChart from './charts/CaptureRateChart.vue'
import LegendaryChart from './charts/LegendaryChart.vue'

let pokemons: Ref<Array<PokemonType>> = ref([])
const generations = computed(() => new Set(pokemons.value.map(pokemon => pokemon.generation)).size)

const types = computed(() => {
  const typeMap = new Map<string, number>()
  pokemons.value.forEach(pokemon => {
    if (typeMap.has(pokemon.type1)) {
      typeMap.set(pokemon.type1, typeMap.get(pokemon.type1)! + 1)
    } else {
      typeMap.set(pokemon.type1, 1)
    }

    if (pokemon.type2) {
      if (typeMap.has(pokemon.type2)) {
        typeMap.set(pokemon.type2, typeMap.get(pokemon.type2)! + 1)
      } else {
        typeMap.set(pokemon.type2, 1)
      }
    }
  })

  return typeMap
})

const heights = computed(() => {
  const sortedByHeight = [...pokemons.value]
  return sortedByHeight.sort((a, b) => {
    return b.height_m - a.height_m
  }).slice(0, 10).map(pokemon =>  {
    return { name: pokemon.name, height: pokemon.height_m }
  }).reverse()
})

const captureRates = computed(() => {
  const captureRates = new Map<number, number>()
  pokemons.value.forEach(pokemon => {
    if (captureRates.has(pokemon.capture_rate)) {
      captureRates.set(pokemon.capture_rate, captureRates.get(pokemon.capture_rate)! + 1)
    } else {
      captureRates.set(pokemon.capture_rate, 1)
    }
  })

  return new Map([...captureRates.entries()].sort((a, b) => a[0] - b[0]))
})

const legendaries = computed(() => {
  return pokemons.value.filter(pokemon => pokemon.is_legendary == 1)
})

fetch('/pokemon.csv', { method: 'GET' })
    .then(res => res.text())
    .then(csv => {
      const data: Array<Array<string>> = (parser.parse(csv).data as string[][])

      // delete useless row
      data.pop()

      const header = data.shift()!

      pokemons.value = data.map(data => {
        const pokemonObj: Record<string, any> = {}
        for(let i = 0; i < header.length; i++) {
          pokemonObj[header[i]] = data[i]
        }

        return pokemonObj as PokemonType
      })
    })
</script>

<style lang="stylus" scoped>
.main
  @apply px-8;

  h4
    @apply text-center;

  .data
    @apply flex flex-row flex-wrap items-center justify-between;
</style>