<script setup>
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';

const router = useRouter()
const route = useRoute();

const getWeatherData = async () => {
  try {
    const accessKey = '2270a39d-a82c-4bf0-a889-ab2c31fc2320';
    const headers = {'X-Yandex-Weather-Key': accessKey};
    const response = await axios.get(`https://api.weather.yandex.ru/v2/forecast?lat=${route.query.lat}&lon=${route.query.lng}`, {headers})

    const hourlyWithTime = response.data.forecasts[0].hours.map((hour) => ({
      ...hour,
      currentTime: new Date(hour.hour_ts * 1000)
    }));

    const forecasts = response.data.forecasts.map((forecast) => ({
      ...forecast,
      date: new Date(forecast.date) 
    }));

    return {
      ...response.data,
      currentTime: new Date(response.data.fact.obs_time * 1000),
      hourly: hourlyWithTime,
      forecasts
    }
  } catch (err) {
    console.log(err);
    return null;
  }
};
const weatherData = await getWeatherData();
console.log(weatherData)


const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  localStorage.setItem('savedCities', JSON.stringify(updatedCities));
  router.push({
    name: 'home'
  })
}
</script>

<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- banner -->
    <div v-if="route.query.preview" class="text-white bg-secondary w-full text-center">
      <p>You are currently previewing this city, click the "+" icon to start tracking this city.</p>
    </div>
    <!-- weather -->
     <div v-if="weatherData?.fact" class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p v-if="weatherData.currentTime">
        {{
          new Date(weatherData.currentTime).toLocaleDateString(
            "en-us",
            {
              weekday: "short",
              day: "2-digit",
              month: "long",
            }
          )
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString(
            "en-us",
            {
              timeStyle: "short",
            }
          )
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(weatherData.fact.temp) }}&deg;
      </p>
      
        <p>Feels like {{ Math.round(weatherData.fact.feels_like) }}&deg;</p>
        <p class=" capitalize">{{ weatherData.fact.condition}}</p>
        <img class=" w-[150px] h-auto" :src="`https://yastatic.net/weather/i/icons/funky/dark/${weatherData.fact.icon}.svg`" alt="">
     </div>

     <hr class=" border-white opacity-10 border w-full">

     <!-- Hourly weather -->
      <div class=" max-w-screen-md w-full py-12">
        <div class=" mx-8 text-white">
          <h2 class="mb-4">Hourly weather</h2>
          <div class="flex gap-10 overflow-x-scroll">
            <div v-for="hourData in weatherData.hourly" :key="hourData.dt" class="flex flex-col gap-4 items-center">
              <p class=" whitespace-nowrap text-md" >
                {{ new Date(hourData.currentTime).toLocaleTimeString("en-us", {hour: "numeric"}) }}
              </p>
              <img
                class="w-auto h-[50px] object-cover"
                :src="
                  `https://yastatic.net/weather/i/icons/funky/dark/${weatherData.fact.icon}.svg`
                "
                alt=""
              />
              <p class="text-xl">
                {{ Math.round(hourData.temp) }}&deg;
              </p>
            </div>
          </div>
        </div>
      </div>

      <!-- week -->
       <div class="max-w-screen-md w-full py-12">
        <div class="mx-8 text-white">
          <h2 class="mb-4">7 day forecast</h2>
          <div v-for="day in weatherData.forecasts" :key="day.date" class="flex items-center">
            <p class="flex-1">
              {{
                new Date(day.date * 1000).toLocaleDateString(
                  "en-us",
                  {
                    weekday: "long",
                  }
                )
              }}
            </p>
            <img
              class="w-[50px] h-[50px] object-cover"
              :src="
                `https://yastatic.net/weather/i/icons/funky/dark/${weatherData.fact.icon}.svg`
              "
              alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>H: {{ Math.round(day.parts.day.temp_max) }}</p>
            <p>L: {{ Math.round(day.parts.day.temp_min) }}</p>
          </div>
          </div>
        </div>
       </div>

       <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500" @click="removeCity">
        <i class="fa-solid fa-trash"></i>
        <p>Remove city</p>
       </div>
  </div>
</template>