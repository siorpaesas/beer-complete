<script>
import { ref, computed } from "vue";

export default {
  setup() {
    let searchTerm = ref("");
    let beers = [];

    const getData = () => {
      fetch("https://api.punkapi.com/v2/beers", {
        headers: {
          "Content-type": "application/json",
        },
      })
        .then((res) => res.json())
        .then((response) => {
          beers = response;
        })
        .catch((error) => {
          console.log(
            "Looks like there was a problem opening the fridge: \n",
            error
          );
        });
    };

    getData();

    const searchBeers = computed(() => {
      if (searchTerm.value === "") {
        return [];
      }

      return beers.filter((beer) => {
        if (beer.name.toLowerCase().includes(searchTerm.value.toLowerCase())) {
          return beer;
        }
      });
    });

    const selectBeer = (beer) => {
      selectedBeer.value = beer;
      searchTerm.value = "";
    };

    let selectedBeer = ref("");

    return {
      beers,
      searchTerm,
      searchBeers,
      selectBeer,
      selectedBeer,
    };
  },
};
</script>

<template>
  <div class="grid bg-gray mt-10">
    <div class="text-larger">
      <div class="mb-3">
        <label for="search" class="w-full pr-6 text-white h-4">Looking for your beer?</label>
      </div>

      <div class="absolute items-center pointer-events-auto" style="z-index:1;">
          <svg class="absolute text-slate-400 h-6 w-6 mt-2 ml-1" viewBox="0 0 20 20" fill="currentColor">
            <path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd"></path>
          </svg>
        </div>
      <input
        type="text"
        id="search"
        v-model="searchTerm"
        placeholder="Search the fridge.."
        class="border-2 pl-8 rounded h-10"
        style="border-color: rgb(135, 117, 40)"
        autocomplete="off"
      />
      </div>

      <ul v-if="searchBeers.length > 0" class="text-white ml-2 mt-4">
        <li class="font-bold mb-3">Showing {{ searchBeers.length }} results</li>
        <li
          v-for="beer in searchBeers"
          :key="beer.name"
          @click="selectBeer(beer.name)"
          class="cursor-pointer border-b border-slate-500 pl-1 pt-1 pb-1 bg-slate-600 last-of-type:border-0 hover:text-slate-300"
        >
          {{ beer.name }}
        </li>
      </ul>

      <div v-if="selectedBeer" class="ml-2 pt-7 text-lg text-white">
        <p class="">Your choice was <span class="font-semibold">{{ selectedBeer }}</span></p>
        <p class="pt-8 text-6xl">Skóll!</p>

      </div>
  </div>
</template>