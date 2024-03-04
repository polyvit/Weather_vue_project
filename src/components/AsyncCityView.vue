<template>
  <div></div>
</template>

<script setup>
import axios from "axios";
import { useRoute } from "vue-router";

const route = useRoute();
const opeweatherApiKey = "6ea716c1d27aebee221e04d81ad335c6";

const getWeatherData = async () => {
  try {
    const { data } = await axios.get(
      `https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=${opeweatherApiKey}`
    );
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = data.current.dt * 1000 + localOffset;
    data.currentTime = utc + 1000 * data.timezone_offset;

    data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * data.timezone_offset;
    });
    return data;
  } catch (e) {
    console.log(e);
  }
};

const weatherData = await getWeatherData();
</script>
