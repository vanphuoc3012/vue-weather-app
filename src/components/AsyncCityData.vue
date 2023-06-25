<script setup lang="ts">
import {useRoute, useRouter} from "vue-router";
import axios, {AxiosResponse} from "axios";
import type {ForecastInterface, ListForecast} from "@/models/weather/ForecastInterface";
import type {WeatherInterface} from "@/models/weather/WeatherInterface";

const API_KEY = "d9f0b85c258a4aae31f6e36cbd81e6cb";

const route = useRoute();
const router = useRouter();
const lat = route.query.lat;
const lon = route.query.lon;

let timezone: number = 0;
const getCurrentWeatherData = async () => {
  try {
    const url = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${API_KEY}&units=metric`

    const response: AxiosResponse<WeatherInterface> = await axios.get(url);

    console.log("Current: ", response);
    timezone = response.data.timezone;
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = response.data.dt * 1000 + localOffset;
    response.data.currentTime = utc + 1000 * timezone;

    return response.data;
  } catch (error) {
    console.error(error);
  }
}

const getForecastWeatherData = async () => {
  try {
    const url = `https://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&appid=${API_KEY}&units=metric`
    const response: AxiosResponse<ForecastInterface> = await axios.get(url);

    console.log("Forecast:", response);
    const localOffset: number = new Date().getTimezoneOffset() * 60000;

    response.data.list.forEach((hour: ListForecast) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * timezone;
    });
    return response.data;
  } catch (error) {
    console.error(error);
  }
}

const currentData: WeatherInterface = await getCurrentWeatherData();
const forecastData: ForecastInterface = await getForecastWeatherData();
const dailyData: ListForecast[] = [];
for (let i = 0; i < forecastData.list.length; i += 8) {
  dailyData.push(forecastData.list[i]);
}

const currentTime = new Date(currentData.currentTime);

const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem('savedCities'));
  const  updatedCities = cities.filter((city) => city.id !== route.params.id)
  localStorage.setItem('savedCities', JSON.stringify(updatedCities));
  router.push({
    name: 'home'
  })
}
</script>

<template>
  <div class="flex flex-col flex-1 items-center">
    <!--    Banner-->
    <div
        class="text-white p-4 bg-weather-secondary w-full text-center"
        v-if="route.query.preview">
      <p>You are currently previewing this city, click the '+' button to start tracking this city.</p>
    </div>

    <!--    Weather overview-->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{ currentTime.toLocaleDateString("en-us", {weekday: "short", day: "2-digit", month: "long"}) }}

        {{ currentTime.toLocaleTimeString("en-us", {timeStyle: "short"}) }}
      </p>
      <p class="text-8xl mb-8">{{ Math.round(currentData.main.temp) }}&deg;C</p>
      <div class="text-center">
        <p>Feel like {{ Math.round(currentData.main.feels_like) }}&deg;C</p>
        <p class="capitalize">{{ currentData.weather[0].description }}</p>
        <img class="w-full h-auto"
             :src="`https://openweathermap.org/img/wn/${currentData.weather[0].icon}@2x.png`"
             :alt="currentData.weather[0].main"/>
      </div>
    </div>

    <!--  3 hour Forecast  -->
    <hr class="border-white w-full border-opacity-30"/>

    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">3h forecast</h2>
        <div class="flex gap-10 overflow-x-scroll">
          <div class="flex flex-col gap-4 items-center"
               v-for="hour3Data in forecastData.list" :key="hour3Data.dt"
          >
            <p class="whitespace-nowrap">
              {{ new Date(hour3Data.currentTime).toLocaleTimeString("en-us", {hour: "numeric"}) }}</p>
            <img class="w-auto h-[50px] object-cover"
                 alt=""
                 :src="`https://openweathermap.org/img/wn/${hour3Data.weather[0].icon}@2x.png`">
            <p>{{ Math.round(hour3Data.main.feels_like) }}&deg;C</p>
          </div>
        </div>
      </div>
    </div>

    <!--  5 days forecast  -->
    <hr class="border-white w-full border-opacity-30"/>
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">5 days forecast</h2>

        <div class="">
          <div class="flex items-center"
               v-for="data in dailyData" :key="data.dt"
          >
            <p class="flex-1">{{ new Date(data.currentTime).toLocaleDateString("en-us", {weekday: "long"}) }}</p>
            <img class="w-auto h-[50px] object-cover"
                 alt=""
                 :src="`https://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`">
            <div class="flex gap-2 flex-1 justify-end">
              <p>H: {{ Math.round(data.main.temp_max) }}&deg;C</p>
              <p>L: {{ Math.round(data.main.temp_min) }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!--    Delete-->
    <div
        class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
        @click="removeCity"
    >
      <i class="fa-regular fa-trash-can text-3xl"></i>
      <p class="text-3xl">Remove city</p>
    </div>
  </div>
</template>
