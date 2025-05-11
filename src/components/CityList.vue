<script setup>
import { ref } from 'vue';
import axios from 'axios';
import CityCard from './CityCard.vue';
import { useRouter } from 'vue-router';

const savedCities = ref([]);
const router = useRouter()

const accessKey = '2270a39d-a82c-4bf0-a889-ab2c31fc2320';
const headers = {'X-Yandex-Weather-Key': accessKey};
const getCities = async () => {
    if(localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'))

        const requests = []
        savedCities.value.forEach((city) => {
            requests.push(axios.get(`https://api.weather.yandex.ru/v2/forecast?lat=${city.coords.lat}&lon=${city.coords.lng}`, {headers}))
        });

        const weatherData = await Promise.all(requests);

        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data
        })
    }
}
await getCities()

const goToCityView = (city) => {
    router.push({
        name: 'cityView',
        params: {state: city.state, city: city.city},
        query: { 
            id: city.id, 
            lat: city.coords.lat, 
            lng: city.coords.lng}
    })
}
</script>

<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)" />
    </div>
</template>