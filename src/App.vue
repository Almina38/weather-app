<template>
  <div class="app">
    <div class="header container h-100 p-5">
      <h1 class="titel">Vue-ther weather</h1>
      <p class="intro">Welcome to the weather-app built by Vue.js 3!</p>
      <div class="d-flex justify-content-center h-100">
        <div class="searchbar w-50 mx-2">
          <input type="text" class="input form-control" v-model="city" placeholder="Enter a city">
        </div>
        <button class="btn-search btn btn-primary" 
        @click="searchWeather">Search <i class="fas fa-search"></i></button>
      </div>
    </div>
    <br>
    <Weather 
    :city="city"
    v-if="showWeather"
    ></Weather>
  </div>
</template>

<script>

import Weather from './components/Weather.vue';

export default (await import('vue')).defineComponent({
  name: 'app',
  components: {
    Weather
  },
  data( )
    {
      return{
        city: '',
        showWeather: false,
      }
    },
    methods:{
      async searchWeather() {
        this.showWeather =false;
        await this.$nextTick();
        this.showWeather = true;
      }
    }
})
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

</style>
