<script>
import { geolocationAccessToken, geolocationApiUrl } from '../config'
import axios from 'axios'
import router from '../router'

export default {
  data() {
    return {
      searchQuery: '',
      searchResults: [],
      searchTimeout: null
    }
  },
  methods: {
    async getSearchResults() {
      if (this.searchQuery.trim().length > 0) {
        const results = await axios.get(
          `${geolocationApiUrl}/${this.searchQuery}.json?access_token=${geolocationAccessToken}&types=place`
        )
        const { features } = results.data
        this.searchResults = features
      }
    },

    changeInput() {
      clearTimeout(this.searchTimeout)
      this.searchTimeout = setTimeout(this.getSearchResults, 500)
    },

    previewCity(result) {
      const [city, state] = result.place_name.split(',')
      router.push({
        name: 'cityView',
        params: { state: state.replaceAll(' ', ''), city },
        query: {
          lat: result.geometry.coordinates[1],
          lng: result.geometry.coordinates[0],
          preview: true
        }
      });
    }
  },

  computed: {
    inputEntered() {
      return this.searchQuery.trim().length > 0
    }
  }
}
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 flex flex-col gap-[1px]">
      <input
        type="text"
        class="py-2 px-1 bg-transparent border-b w-full focus:border-weather-secondary focus:outline-none"
        placeholder="search a city"
        v-model="searchQuery"
        @input="changeInput"
      />
      <p
        v-if="searchResults.length === 0 && inputEntered"
        class="py-2 mx-2 cursor-pointer border-b border-weather-primary"
      >
        No results match your query
      </p>
      <ul v-if="inputEntered" class="bg-weather-secondary text-white text-sm">
        <li
          v-for="result in searchResults"
          :key="result.id"
          class="py-2 mx-2 cursor-pointer border-b border-weather-primary"
          @click="previewCity(result)"
        >
          {{ result.place_name }}
        </li>
      </ul>
    </div>
  </main>
</template>
