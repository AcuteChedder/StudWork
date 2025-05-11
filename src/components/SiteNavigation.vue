<script setup>
import { ref } from 'vue';
import {uid} from 'uid'
import { RouterLink, useRoute, useRouter } from 'vue-router';

const savedCities = ref([])
const route = useRoute();
const router = useRouter()
const addCity = () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
    }

    const locationObj = {
        id: uid(),
        state: route.params.state,
        city: route.params.city,
        coords: {
            lat: route.query.lat,
            lng: route.query.lng
        }
    };

    savedCities.value.push(locationObj);
    localStorage.setItem('savedCities', JSON.stringify(savedCities.value))

    let query = Object.assign({}, route.query);
    delete query.preview;
    query.id = locationObj.id;
    router.replace({query});
}

</script>

<template>
    <header class=" sticky top-0 bg-primary shadow-lg">
        <nav class="flex flex-col sm:flex-row items-center gap-4 text-white py-6 mx-8">
            <RouterLink :to="{name: 'home'}">
                <div class="flex items-center gap-3 flex-1">
                    <i class="fa-solid fa-sun text-2xl"></i>
                    <p class="text-2xl">The Local Weather</p>
                </div>
            </RouterLink>

            <div class="flex gap-3 flex-1 justify-end">
                <i class="fa-solid fa-circle-info text-xl hover:text-secondary duration-150 cursor-pointer"></i>
                <i class="fa-solid fa-plus text-xl hover:text-secondary duration-150 cursor-pointer" @click="addCity()" v-if="route.query.preview"></i>
            </div>

        </nav>
    </header>
</template>