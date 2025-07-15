<template>
  <div class="app">
    <div class="header container h-100 p-4 position-relative">
  <img
    src="/img/titel.png"
    alt="VueTher"
    class="titel-img"
  />
      <p class="intro">Welcome to the weather-app built by Vue.js 3!</p>
      <div class="d-flex justify-content-center h-100">
        <div class="searchbar w-50 mx-2 position-relative">
          <input
            ref="searchInput"
            type="text"
            class="input form-control"
            v-model="city"
            placeholder="Enter a city"
            @input="fetchSuggestions"
            @keyup.enter="searchWeather"
            autocomplete="off"
          />
        </div>
        <button class="btn-search btn btn-primary" @click="searchWeather">
          Search <i class="fas fa-search"></i>
        </button>
      </div>
    </div>

    <br />
    <Weather :city="city" v-if="showWeather" />

    <!-- Zwevende suggesties -->
    <ul
      v-if="suggestions.length"
      class="suggestions-list"
      :style="{
        position: 'fixed',
        top: suggestionPos.top + 'px',
        left: suggestionPos.left + 'px',
        width: suggestionPos.width + 'px'
      }"
    >
      <li
        v-for="(suggestion, index) in suggestions"
        :key="index"
        @click="selectSuggestion(suggestion)"
      >
        {{ suggestion }}
      </li>
    </ul>
  </div>
</template>

<script>
import Weather from './components/Weather.vue';

export default {
  name: 'app',
  components: { Weather },
  data() {
    return {
      city: 'Leiden',
      showWeather: true,
      suggestions: [],
      suggestionPos: { top: 0, left: 0, width: 0 }
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
    },
    handleClickOutside(event) {
      const searchInput = this.$refs.searchInput;
      const suggestionsList = this.$el.querySelector('.suggestions-list');
      if (
        !searchInput.contains(event.target) &&
        (!suggestionsList || !suggestionsList.contains(event.target))
      ) {
        this.suggestions = [];
      }
    }
  },
  watch: {
    suggestions(val) {
      if (val.length) {
        this.$nextTick(() => {
          const inputEl = this.$refs.searchInput;
          const rect = inputEl.getBoundingClientRect();
          this.suggestionPos = {
            top: rect.bottom + window.scrollY,
            left: rect.left + window.scrollX,
            width: rect.width
          };
        });
      }
    }
  },
  mounted() {
    document.addEventListener('click', this.handleClickOutside);
  },
  beforeUnmount() {
    document.removeEventListener('click', this.handleClickOutside);
  }
};
</script>

<style scoped>
html, body {
  margin: 0;
  padding: 0;
}
.titel-img {
  max-width: 300px;  
  max-height: 80px;  
  width: 100%;      
  height: auto;     
  display: block;
  margin: 0 auto 0.5rem auto; 
}


.header {
  background: rgba(42, 42, 64, 0.85);
  backdrop-filter: blur(5px);
  border-radius: 20px;
  color: #f5f5f5;
  text-align: center;
  font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  margin-top: 1rem;
  width: 80%;
}

.btn-search {
  background-color: #4fd1c5;
  color: #1e1e2f;
  border: none;
  border-radius: 8px;
  font-weight: 600;
}

.btn-search:hover {
  background-color: #38b2ac;
}

.titel {
  font-size: 1.9rem;

}

.intro {
  font-size: 0.9rem;
  font-style: italic;
}

.searchbar {
  position: relative;
  z-index: 100;
}

.suggestions-list {
  list-style: none;
  margin: 0;
  padding: 0;
  background: rgba(42, 42, 64, 0.85);
  backdrop-filter: blur(5px);
  border-radius: 5px;
  max-height: 150px;
  overflow-y: auto;
  z-index: 9999;
  color: #f5f5f5;
  border: 1px solid #555;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.5);
}

.suggestions-list li {
  padding: 8px 12px;
  cursor: pointer;
}

.suggestions-list li:hover {
  background-color: #3a506b;
}

@media (max-width: 768px) {
  .searchbar.w-50 {
    width: 64% !important;  
  }

  .titel-img {
    max-width: 200px;
    max-height: 60px;
  }

  .titel {
    display: none;
  }
  .titel{
    font-size: 1.2rem;
  }

  .searchbar .input {
    font-size: 0.9rem;      
    padding: 6px 10px;     
  }

  .btn-search {
    font-size: 0.9rem;     
    padding: 6px 12px;      
  }
   .btn-search i.fas.fa-search {
    font-size: 0.8rem;
  }
}

</style>
