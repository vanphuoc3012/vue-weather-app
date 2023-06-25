<script setup lang="ts">
import {ref} from "vue";
import axios from "axios";
import CityCard from "@/components/CityCard.vue";
import type {WeatherInterface} from "@/models/weather/WeatherInterface";
import {useRouter} from "vue-router";

const API_KEY = "d9f0b85c258a4aae31f6e36cbd81e6cb";
const router = useRouter();

const savedCities = ref([]);
const weatherData: WeatherInterface[] = [];

const getCities = async () => {
  if (localStorage.getItem('savedCities')) {
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
    console.log(savedCities.value)
  }

  const requests = [];
  savedCities.value.forEach((city) => {
    const url: string = `https://api.openweathermap.org/data/2.5/weather?lat=${city.coord.lat}&lon=${city.coord.lon}&appid=${API_KEY}&units=metric`;
    requests.push(axios.get(url))
  })

  const weatherDataResponse = await Promise.all(requests);
  weatherDataResponse.forEach((value) => {
        weatherData.push(value.data)
      }
  )
}

await getCities();

const goToCityView = (weather: WeatherInterface) => {
  router.push({
    name: 'cityView',
    params: {
      city: weather.name,
      id: weather.id
    },
    query: {
      lat: weather.coord.lat,
      lon: weather.coord.lon
    }
  })
}
</script>

<template>
  <div v-for="weather in weatherData" :key="weather.id">
    <CityCard :weather="weather" @click="goToCityView(weather)"></CityCard>
  </div>
</template>
