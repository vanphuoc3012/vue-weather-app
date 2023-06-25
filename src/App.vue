<script setup lang="ts">
import NavBar from "@/components/NavBar.vue";
</script>

<template>
 <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
   <NavBar></NavBar>
   <RouterView></RouterView>
 </div>
</template>

<style scoped>

</style>
