<script setup lang="ts">
import cities from "../../public/lib/city.list.json";
import type {CityInterface} from "@/models/cities/CityInterface";
import {ref} from "vue";
import {useRouter} from "vue-router";
import CityList from "@/components/CityList.vue";

const Cities = cities as CityInterface[];

const NUM_SUGGESTION: number = 10;

const searchQuery = ref<String>("");
const queryTimeout = ref<typeof setTimeout>(null);
const mapBoxSearchResults = ref<CityInterface[]>([]);
const notFound = ref<Boolean>(false);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(() => {
    if (searchQuery.value !== "") {
      mapBoxSearchResults.value = Cities.filter(city =>
          city.name.toLowerCase().includes(searchQuery.value.toLowerCase().trim()) || city.state.toLowerCase().includes(searchQuery.value.toLowerCase().trim())
      )
      //     .sort((c1, c2) => {
      //   return c1.name.toLowerCase().localeCompare(c2.name.toLowerCase());
      // })
          .slice(0, NUM_SUGGESTION);
      notFound.value = mapBoxSearchResults.value.length === 0;
      return;
    }
    mapBoxSearchResults.value = [];
  }, 300)
};

const router = useRouter();
const previewCity = (searchResult: CityInterface) => {
  console.log(searchResult);
  const {name, state, id} = searchResult;
  const {lat, lon} = searchResult.coord;
  console.log(name, state, lat, lon)
  router.push({
    name: 'cityView',
    params: {
      state: state,
      city: name,
      id: id
    },
    query: {
      lat: lat,
      lon: lon,
      preview: true
    }
  })
}
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
          @input="getSearchResults"
          v-model="searchQuery"
          class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-md focus:shadow-amber-50 rounded"
          type="text" placeholder="Search for a city or state"
      >
      <p class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
         v-if="notFound">No result match your query, please try different term</p>
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
          v-if="mapBoxSearchResults.length !== 0"
      >
        <li
            v-for="searchResult in mapBoxSearchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer hover:bg-gray-500"
            @click="previewCity(searchResult)"
        >{{ searchResult.name }}
        </li>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList></CityList>
        <template #fallback>
          <p class="text-white">Loading...</p>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<style scoped>

</style>