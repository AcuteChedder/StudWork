<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

const searchQuery = ref("")
const queryTimeout = ref(null)
const mapboxAPIKey = "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q"
const mapboxSearchResults = ref(null)
const router = useRouter()

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
        mapboxSearchResults.value = result.data.features
        console.log(mapboxSearchResults.value)
      } catch (error) {
        searchError.value = true
      }
      
      return
    }
    mapboxSearchResults.value = null
  }, 300)
}

const previewCity = (searchResult) => {
  console.log(searchResult)
  const [city, state] = searchResult.place_name.split(",")
  router.push({
    name: 'cityView',
    params: {
      state: state.replaceAll(" ", ""),
      city: city
    },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true
    }
  })
}

</script>

<template>
  <main class=" w-container text-white">
    <div class=" pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="search for a city or state" class=" py-2 px-1 w-full bg-transparent border-b focus:border-secondary focus:outline-none focus:shadow-md duration-200">
      <ul class=" absolute bg-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapboxSearchResults">
        <p v-if="searchError">Sorry, something wrong, try again</p>
        <p v-if="!serverError && mapboxSearchResults.length === 0">no results</p>
        <div v-else>
          <li @click="previewCity(searchResult)" v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer">{{ searchResult.place_name }}</li>
        </div>
      </ul>
    </div>
  </main>
</template>
