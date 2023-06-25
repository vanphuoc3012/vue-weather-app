<script setup lang="ts">

import AsyncCityData from "@/components/AsyncCityData.vue";
</script>

<template>
<div>
  <Suspense>
    <AsyncCityData/>
    <template #fallback>
      <p class="text-white pt-4">Loading...</p>
    </template>
  </Suspense>
</div>
</template>
