<template>
  <div>
    <div class="flex justify-center py-4 text-white text-xl" v-if="errMsg">
      {{ errMsg }}
    </div>
    <Suspense>
      <AsyncCityView />
      <template #fallback>
        <CityViewSkeleton />
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCityView from "../components/AsyncCityView.vue";
import CityViewSkeleton from "../components/CityViewSkeleton.vue";
import { ref, onErrorCaptured } from "vue";

const errMsg = ref(null);
onErrorCaptured((e) => {
  errMsg.value = "It seems like you've not turned the vpn on";
  return true;
});
</script>
