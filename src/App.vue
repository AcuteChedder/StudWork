<script setup>
import { RouterLink, RouterView } from 'vue-router'
import SiteNavigation from "./components/SiteNavigation.vue"

</script>

<template>
  <div class=" mx-32 font-roboto">
    <SiteNavigation />

    <RouterView />
  </div>
</template>

<style scoped>

</style>
