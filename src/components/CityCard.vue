<script setup>
defineProps({
    city: {
        type: Object,
        default: () => {}
    }
})
</script>

<template>
    <div class="flex py-6 px-3 bg-secondary rounded-md shadow-md cursor-pointer">
        <div class="flex flex-col flex-1">
            <h2 class="text-3xl">{{ city.city }}</h2>
            <h3>{{ city.state }}</h3>
        </div>

        <div class="flex flex-col gap-2">
            <p class="text-3xl self-end mt-4">{{ Math.round(city.weather.fact.temp) }}&deg;</p>
            <div class="flex gap-2">
                
            </div>
        </div>
    </div>
</template>