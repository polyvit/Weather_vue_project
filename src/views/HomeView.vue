<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";

const router = useRouter();
const mapboxApiKey =
  "pk.eyJ1IjoicG9saW5hMzc1IiwiYSI6ImNsdGN0ZjRxOTAzbmgya285aTJ1enB4MXUifQ.9ChwbB6ofV4-rnpq9IE3Fw";
const searchQuery = ref("");
const queryTimeout = ref(null);
const searchResults = ref(null);
const error = ref(null);

const getSearchResult = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value.trim().length) {
      try {
        const { data } = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxApiKey}&types=place`
        );
        searchResults.value = data.features;
      } catch (e) {
        error.value = true;
      }
      return;
    }
    searchResults.value = null;
  }, 500);
};

const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(", ");
  router.push({
    name: "city",
    params: {
      state: state.replaceAll(" ", ""),
      city,
    },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    },
  });
};
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResult"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        v-if="searchResults"
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
      >
        <p v-if="error">Sorry, something went wrong, please try again</p>
        <p v-if="!error && searchResults.length === 0">
          No results match your query
        </p>
        <template v-else>
          <li
            v-for="result in searchResults"
            :key="result.id"
            class="py-2 cursor-pointer"
            @click="previewCity(result)"
          >
            {{ result.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback><CityCardSkeleton /></template>
      </Suspense>
    </div>
  </main>
</template>
