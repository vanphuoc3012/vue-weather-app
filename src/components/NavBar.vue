<script setup lang="ts">

import BaseModal from "@/components/BaseModal.vue";
import {ref} from "vue";
import {useRoute, useRouter} from "vue-router";
import type {CityInterface} from "@/models";

const modalActive = ref(false);
const toggleModal = () => {
  modalActive.value = !modalActive.value;
}

const router = useRouter();
const route = useRoute();
const savedCities = ref<CityInterface[]>([]);
const addCity = () => {
  if (localStorage.getItem('savedCities')) {
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
  }

  const locationObj: CityInterface = {
    id: route.params.id,
    state: route.params.state,
    city: route.params.city,
    coord: {
      lat: route.query.lat,
      lon: route.query.lon
    }
  }

  savedCities.value.push(locationObj);
  localStorage.setItem('savedCities', JSON.stringify(savedCities.value))

  let query = Object.assign({}, route.query);
  delete query.preview;
  router.replace({query});
}
</script>

<template>
  <header>
    <nav class="bg-gray-800">
      <div class="mx-auto max-w-7xl px-2 sm:px-6 lg:px-8">
        <div class="relative flex h-16 items-center justify-between">
          <div class="absolute inset-y-0 left-0 flex items-center sm:hidden">
            <!-- Mobile menu button-->
            <button type="button"
                    class="inline-flex items-center justify-center rounded-md p-2 text-gray-400 hover:bg-gray-700 hover:text-white focus:outline-none focus:ring-2 focus:ring-inset focus:ring-white"
                    aria-controls="mobile-menu" aria-expanded="false">
              <span class="sr-only">Open main menu</span>
              <!--
                Icon when menu is closed.

                Menu open: "hidden", Menu closed: "block"
              -->
              <svg class="block h-6 w-6" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
                   aria-hidden="true">
                <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5"/>
              </svg>
              <!--
                Icon when menu is open.

                Menu open: "block", Menu closed: "hidden"
              -->
              <svg class="hidden h-6 w-6" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
                   aria-hidden="true">
                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>
          <div class="flex flex-1 items-center justify-center sm:items-stretch sm:justify-start">
            <div class="flex flex-shrink-0 items-center">
              <a href="/" class="text-white text-xl">
                <i class="fa-solid fa-cloud-sun-rain text-3xl text-white"></i>
                Weather
              </a>
            </div>
            <div class="hidden sm:ml-6 sm:block">
              <div class="flex space-x-4">
                <!-- Current: "bg-gray-900 text-white", Default: "text-gray-300 hover:bg-gray-700 hover:text-white" -->
                <a href="#" class="bg-gray-900 text-white rounded-md px-3 py-2 text-sm " aria-current="page">Home</a>
                <a href="#"
                   class="text-gray-300 hover:bg-gray-700 hover:text-white rounded-md px-3 py-2 text-sm ">Team</a>
                <a href="#"
                   class="text-gray-300 hover:bg-gray-700 hover:text-white rounded-md px-3 py-2 text-sm ">Projects</a>
                <a href="#"
                   class="text-gray-300 hover:bg-gray-700 hover:text-white rounded-md px-3 py-2 text-sm ">Calendar</a>
              </div>
            </div>
          </div>
          <div class="absolute inset-y-0 right-0 flex items-center pr-2 sm:static sm:inset-auto sm:ml-6 sm:pr-0">
            <i @click="addCity"
               v-if="route.query.preview"
                class="fa-solid fa-circle-plus text-3xl text-gray-300 p-1 hover:text-white cursor-pointer"></i>
            <i
                class="fa-solid fa-circle-info text-3xl text-gray-300 p-1 hover:text-white cursor-pointer"
            @click="toggleModal"></i>

            <!-- Profile dropdown -->
          </div>
        </div>
      </div>

      <!-- Mobile menu, show/hide based on menu state. -->
      <div class="sm:hidden" id="mobile-menu">
        <div class="space-y-1 px-2 pb-3 pt-2">
          <!-- Current: "bg-gray-900 text-white", Default: "text-gray-300 hover:bg-gray-700 hover:text-white" -->
          <a href="#" class="bg-gray-900 text-white block rounded-md px-3 py-2 text-base "
             aria-current="page">Dashboard</a>
          <a href="#"
             class="text-gray-300 hover:bg-gray-700 hover:text-white block rounded-md px-3 py-2 text-base ">Team</a>
          <a href="#"
             class="text-gray-300 hover:bg-gray-700 hover:text-white block rounded-md px-3 py-2 text-base ">Projects</a>
          <a href="#"
             class="text-gray-300 hover:bg-gray-700 hover:text-white block rounded-md px-3 py-2 text-base ">Calendar</a>
        </div>
      </div>
    </nav>
    <BaseModal :modal-active="modalActive" @close-modal="toggleModal">
      <div class="text-black">
        <h1 class="text-2xl mb-1">About:</h1>
        <p class="mb-4">
          The Local Weather allows you to track the current and
          future weather of cities of your choosing.
        </p>
        <h2 class="text-2xl">How it works:</h2>
        <ol class="list-decimal list-inside mb-4">
          <li>
            Search for your city by entering the name into the
            search bar.
          </li>
          <li>
            Select a city within the results, this will take
            you to the current weather for your selection.
          </li>
          <li>
            Track the city by clicking on the "+" icon in the
            top right. This will save the city to view at a
            later time on the home page.
          </li>
        </ol>

        <h2 class="text-2xl">Removing a city</h2>
        <p>
          If you no longer wish to track a city, simply select
          the city within the home page. At the bottom of the
          page, there will be am option to delete the city.
        </p>
      </div>
    </BaseModal>
  </header>

</template>

<style scoped>

</style>