<script>
import { apiAccessToken, apiUrl } from '../config'
import axios from 'axios'

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
          `${apiUrl}/${this.searchQuery}.json?access_token=${apiAccessToken}&types=place`
        )
        const { features } = results.data
        this.searchResults = features
      }
    },

    changeInput() {
      clearTimeout(this.searchTimeout)
      this.searchTimeout = setTimeout(this.getSearchResults, 500)
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
      <ul v-if="inputEntered" class="bg-weather-secondary text-white text-sm">
        <li
          v-for="result in searchResults"
          :key="result.id"
          class="py-2 mx-2 cursor-pointer border-b border-weather-primary"
        >
          {{ result.place_name }}
        </li>
      </ul>
    </div>
  </main>
</template>
