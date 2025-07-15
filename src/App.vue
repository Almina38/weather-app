<template>
  <div class="app">
    <div class="header container h-100 p-5 position-relative">
      <h1 class="titel">Vue-ther weather</h1>
      <p class="intro">Welcome to the weather-app built by Vue.js 3!</p>
      <div class="d-flex justify-content-center h-100">
        <div class="searchbar w-50 mx-2 position-relative">
          <input
            type="text"
            class="input form-control"
            v-model="city"
            placeholder="Enter a city"
            @input="fetchSuggestions"
            @keyup.enter="searchWeather"
            autocomplete="off"
          />
          <ul v-if="suggestions.length" class="suggestions-list">
            <li v-for="(suggestion, index) in suggestions" :key="index" @click="selectSuggestion(suggestion)">
              {{ suggestion }}
            </li>
          </ul>
        </div>
        <button class="btn-search btn btn-primary" @click="searchWeather">
          Search <i class="fas fa-search"></i>
        </button>
      </div>
    </div>
    <br />
    <Weather :city="city" v-if="showWeather" />
  </div>
</template>

<script>
import Weather from './components/Weather.vue';

export default {
  name: 'app',
  components: { Weather },
  data() {
    return {
      city: '',
      showWeather: false,
      suggestions: []
    };
  },
  methods: {
    async searchWeather() {
      if (!this.city) return;
      this.showWeather = false;
      await this.$nextTick();
      this.showWeather = true;
      this.suggestions = [];
    },
    async fetchSuggestions() {
      if (this.city.length < 3) {
        this.suggestions = [];
        return;
      }
      const apiKey = '4ce1eea15ac17ef2217657495f8e4622';
      const limit = 5;
      const url = `https://api.openweathermap.org/geo/1.0/direct?q=${this.city}&limit=${limit}&appid=${apiKey}`;

      try {
        const response = await fetch(url);
        const data = await response.json();
        this.suggestions = data.map(item => `${item.name}, ${item.country}`);
      } catch (e) {
        this.suggestions = [];
      }
    },
    selectSuggestion(suggestion) {
      this.city = suggestion;
      this.suggestions = [];
      this.searchWeather();
    }
  }
};
</script>


<style scoped>

html, body{
  margin: 0;
  padding: 0;
  background-color: #121212;
}


.header {
  background-color: #212730;
  border-radius: 20px;
  color: #fff;
  text-align: center;
  font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  margin-top: 1rem;
  width: 80%;
}

.btn-search{
  background-image: linear-gradient(to right, cyan, magenta);
}
.titel{
  font-size: 1.7rem;
}
.intro{
  font-size: 0.9rem;
  font-style: italic;
}

.suggestions-list {
  list-style: none;
  margin: 0;
  padding: 0;
  background: #212730;
  border-radius: 5px;
  max-height: 150px;
  overflow-y: auto;
  position: absolute;
  width: 100%;
  z-index: 10;
  color: white;
  border: 1px solid #555;  /* <-- hier border toegevoegd */
  box-shadow: 0 2px 5px rgba(0,0,0,0.5); /* optionele schaduw voor diepte */
}


.suggestions-list li {
  padding: 8px 12px;
  cursor: pointer;
}

.suggestions-list li:hover {
  background-color: #34495e;
}
</style>
