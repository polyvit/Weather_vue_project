<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCityView(city)" />
  </div>
  <p v-if="savedCities.length === 0">
    No locations added. Search to start tracking
  </p>
</template>

<script setup>
import { ref } from "vue";
import CityCard from "./CityCard.vue";
import axios from "axios";
import { useRouter } from "vue-router";

const savedCities = ref([]);
const router = useRouter();
const opeweatherApiKey = "6ea716c1d27aebee221e04d81ad335c6";

const getCitites = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
    const requests = [];
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${opeweatherApiKey}`
        )
      );
    });
    const weatherData = await Promise.all(requests);

    await new Promise((res) => setTimeout(res, 1000));

    weatherData.forEach(({ data }, i) => {
      savedCities.value[i].weather = data;
    });
    console.log(savedCities.value);
  }
};
await getCitites();

const goToCityView = (city) => {
  router.push({
    name: "city",
    params: { state: city.state, city: city.city },
    query: { lat: city.coords.lat, lng: city.coords.lng, id: city.id },
  });
};
</script>
