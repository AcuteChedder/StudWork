<script setup>
import { ref } from 'vue';
import axios from 'axios';

const searchQuery = ref("")
const queryTimeout = ref(null)
const mapboxAPIKey = "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q"
const mapboxSearchResults = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
      mapboxSearchResults.value = result.data.features
      console.log(mapboxSearchResults.value)
      return
    }
    mapboxSearchResults.value = null
  }, 300)
}
</script>

<template>
  <main class=" w-container text-white">
    <div class=" pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="search for a city or state" class=" py-2 px-1 w-full bg-transparent border-b focus:border-secondary focus:outline-none focus:shadow-md duration-200">
      <ul class=" absolute bg-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapboxSearchResults">
        <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer">{{ searchResult.place_name }}</li>
      </ul>
    </div>
  </main>
</template>
