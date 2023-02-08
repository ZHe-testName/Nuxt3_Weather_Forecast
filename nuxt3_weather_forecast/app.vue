<script setup lang="ts">
const {data, error} = useFetch('https://api.openweathermap.org/data/2.5/weather?q=huston&units=metric&appid=20db828dcfeea04d3c91f26150c115c5');

</script>

<template>
  <div class="h-screen relative overflow-hidden">
    <img />

    <pre>
      {{ data }}
      {{ error }}
    </pre>
    <div class="absolute w-full h-full top-0 overlay"></div>

    <div class="absolute w-full h-full top-0 p-48">
      <div class="flex justify-between">
        <div>
          <h1 class="text-7xl text-white">Toronto</h1>

          <p class="font-extralight text-2xl mt-2 text-white">Sunday 9th december</p>

          <img class="w-56 icon"/>
        </div>

        <div>
          <p class="text-9xl text-white font-extralight">53°</p>
        </div>
      </div>

      <div class="mt-20">
        <input 
          type="text"
          class="w-1/2 h-10"
          placeholder="Search a city..."
        />

        <button class="bg-sky-400 w-20 text-white h-10">
          Search
        </button>
      </div>
    </div>
  </div>
</template>

<style>
  .overlay {
    background-color: rgba(0, 0, 0, 0.5);
  }
</style>
