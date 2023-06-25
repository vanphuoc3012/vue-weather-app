<script setup lang="ts">
import type {WeatherInterface} from "@/models/weather/WeatherInterface";
import type {PropType} from "vue";
defineProps({
  weather: {
    type: Object as PropType<WeatherInterface>,
    default: () => {}
  },
})
</script>

<template>
<div class="flex py-6 px-4 bg-weather-secondary rounded-md shadow-md cursor-pointer hover:bg-gray-500">
  <div class="flex flex-col flex-1">
    <h2 class="text-3xl">{{ weather.name }}</h2>
  </div>

  <div class="flex flex-col gap-2">
    <p class="text-3xl self-end"> {{Math.round(weather.main.temp)}}&deg;C</p>
    <div class="flex gap-2">
      <span class="text-xs">H: {{Math.round(weather.main.temp_max)}}&deg;C</span>
      <span class="text-xs">L: {{Math.round(weather.main.temp_min)}}&deg;C</span>
    </div>
  </div>
</div>
</template>
