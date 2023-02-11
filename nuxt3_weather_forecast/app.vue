<script setup lang="ts">
import { AsyncData } from '#app';
import { FetchError } from 'ofetch';

const today = new Date()
  .toLocaleDateString('en-US', {
    weekday: 'long',
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  });

const cookie = useCookie('city');
const config = useRuntimeConfig();

if (!cookie.value) cookie.value = 'kyiv';

const search = ref(cookie.value);
const inputDirty = ref('');
const background = ref('');

const input = computed(() => {
  return inputDirty.value
    .toLowerCase()
    .trim()
    .split(' ')
    .join('+');
});

// it useful when we dont need to do something wit response
// const {data: city, error}: AsyncData<any, FetchError<any> | null> = useFetch(
//   // useFetch takes string or function in first param
//   // in function case useFetch will be recall every time when passed string is changed
//   () => `https://api.openweathermap.org/data/2.5/weather?q=${search.value}&units=metric&appid=${API_KEY}`
// );

// in other case we may to use useAsyncData hook
// to trigger the refetch in this case we have to ways to do that
// 1 - is in returned objet we have to extract refetch method
// and use it weever we want
// 2 - is use the watch property in third parameter
// what we passed into uaeAsyncData hook
const {data: city, error}: AsyncData<any, FetchError<any> | null> = useAsyncData('city', async () => {
  let response: any;

  try {
    response = await $fetch(`https://api.openweathermap.org/data/2.5/weather?q=${search.value}`, {
      params: {
        units: 'metric',
        appid: config.public.secretKey,
      }
    });

    cookie.value = search.value;

    const temp = response.main.temp;

    if (temp < -10) {
      background.value = 
        "https://images.unsplash.com/photo-1483664852095-d6cc6870702d?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=3540&q=80";
    } else if (temp >= -10 && temp <= 0) {
      background.value =
      "https://images.unsplash.com/photo-1476820865390-c52aeebb9891?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=3540&q=80"
    } else if (temp > 0 && temp <= 10) {
      background.value =
      "https://images.unsplash.com/photo-1560258018-c7db7645254e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=4032&q=80";
    } else {
      background.value =
      "https://images.unsplash.com/photo-1507525428034-b723cf961d3e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=3546&q=80";
    };
  } catch (error) {
    
  } finally {
    return response;
  }
}, {
  watch: [search],
});

function searchHandler() {
  search.value = input.value;

  inputDirty.value = '';
};

function goBack() {
  if (cookie.value) {
    search.value = cookie.value;
  };
};
</script>

<template>
  <div 
    v-if="city"
    class="h-screen relative overflow-hidden"
  >
    <img 
      :src="background"
    />

    <div class="absolute w-full h-full top-0 overlay"></div>

    <div class="absolute w-full h-full top-0 p-48">
      <div class="flex justify-between">
        <div>
          <h1 class="text-7xl text-white">{{ city?.name }}</h1>

          <p class="font-extralight text-2xl mt-2 text-white">{{ today }}</p>

          <img 
            class="w-52 icon weather_icon"
            :src="`http://openweathermap.org/img/wn/${city?.weather[0].icon}@4x.png`"
          />
        </div>

        <div>
          <p class="text-9xl text-white font-extralight">{{ city?.main.temp }}°</p>
        </div>
      </div>

      <div class="mt-5">
        <input 
          v-model="inputDirty"
          type="text"
          class="w-1/2 h-10"
          placeholder="Search a city..."
        />

        <button 
          class="bg-sky-400 w-20 text-white h-10"
          @click="searchHandler"
        >
          Search
        </button>
      </div>
    </div>
  </div>

  <div 
    v-else
    class="p-10"
  >
    <h1
      class="text-7xl "
    >
      Ooops, we cant find this city.
    </h1>

    <button
      class="mt-5 bg-sky-400 px-10 w-50 text-white h-10"
      @click="goBack"
    >
      Go back
    </button>
  </div>
</template>

<style>
  .overlay {
    background-color: rgba(0, 0, 0, 0.5);
  }

  .weather_icon {
    margin-left: -2.5rem;
  }
</style>
