<template>
  <div class="container p-0">
    <div class="d-flex">
      <div class="card main-div w-100">
        <div class="p-3">
          <h2 class="today">Today</h2>
          <p class="text light date mb-0">{{ date }}</p>
          <small>{{time}}</small>
          <h2 class="place">
            <i class="fa fa-location"></i> {{ name }} <small>{{ country }}</small>
          </h2>
          <div class="temp">
            <h1 class="weather-temp">{{ temperature }}&deg;</h1>
            <h2 class="text-light">{{ description }} <img :src="iconUrl"></h2>
          </div>
        </div>
      </div>
    <div class="card card-2 w-100">
      <table class="m-4">
        <tbody>
          <tr>
            <th>Min Temp</th>
            <td>{{ temp_min }}&deg;C</td>
          </tr>
          <tr>
            <th>Max Temp</th>
            <td>{{ temp_max }}&deg;C</td>
          </tr>
          
          <tr>
            <th>Humidity</th>
            <td>{{ humidity }}%</td>
          </tr>
          <tr>
            <th>Wind</th>
            <td>{{wind}}</td>
          </tr>
        </tbody>
      </table>
      <DaysWeather :cityname="cityname"></DaysWeather>
      <div id="div_Form" class="d-flex m-3 justify-content-center">
        <form action="">
          <input type="button" value="Change Location" @click="changeLocation" class="btn change_btn btn-primary">
        </form>
      </div>
    </div>
  </div>

    
  </div>
</template>

<script>
import axios from 'axios'
import DaysWeather from './DaysWeather.vue';

export default (await import('vue')).defineComponent({
  name: 'myWeather',
  components: {
    DaysWeather
  },
  props:{
    city: String,
  },
  data(){
    return{
      cityname: this.city,
      temperature: null,
      description: null,
      iconUrl: null,
      date: null,
      time: null,
      name: null,
      //sea_level: null,
      temp_min: null,
    temp_max: null,
      wind: null,
      humidity:null,
      country: null,
      monthNames: ["January","February","March","April","May","June","July",
            "August","September","October","November","December"],
    }
  },
  methods:{
    changeLocation(){
      window.location.reload()
    }
  },
  async created(){
    const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=4ce1eea15ac17ef2217657495f8e4622`);
    const weatherData = response.data;
    this.temperature = Math.round(weatherData.main.temp);
    this.description = weatherData.weather[0].description;
    this.name = weatherData.name;
    this.wind =weatherData.wind.speed;
    //this.sea_level = weatherData.main.sea_level;
    this.country = weatherData.sys.country;
    this.humidity = weatherData.main.humidity;
    this.temp_min = Math.round(weatherData.main.temp_min);
    this.temp_max = Math.round(weatherData.main.temp_max);

    
    this.iconUrl = `https://api.openweathermap.org/img/w/${weatherData.weather[0].icon}.png`;
    const d = new Date();
    this.date = d.getDate() + ' ' + this.monthNames[d.getMonth()] + ' ' + d.getFullYear();
    this.time = d.getHours() + ':' +  d.getMinutes()  ;
    console.log(weatherData);
  }
})
</script>

<style scoped>
body{
  background-color: #343d4b;
 
}

.weather-temp{
  margin: 0;
  font-weight: 700;
  font-size: 4em;
}

h2.today{
  font-size: 3rem;
  font-weight: 400;
}

.main-div{
  border-radius: 20px;
  color: #fff;
  background-image: url('./img/background.png');  
  background-size: cover;
  background-position: center;
  background-blend-mode: overlay;
  background-color: rgb(0, 0, 0, 0.5);
  background-repeat: no-repeat;
  margin-left: 60px;
 
}

.temp{
  position: absolute;
  bottom: 0;
}



.card-2{
  background-color: #212730;
  border-radius: 20px;
  margin-right: 60px;
  max-height: 480px;
}

/* h2, p{
padding: 0 20px 0;
}*/ 

.card-details{
  margin-left: 19px;
}

.h1_left{
  position: absolute;
  bottom: 25px;
  left: 16px;
  font-size: 3vw;
  line-height: 1.2;
}

.h3_left{
  position: absolute;
  left: 16px;
  font-size: 2vw;
  line-height: 0.5;
}

.h3_left small {
  font-size: 1rem;
}

table{
  position: relative;
  left: 15px;
  border-collapse: separate;
  border-spacing: 15px;
  width: 85%;
  text-align: left;
  max-width: 600px;
  margin: 0 auto;
}

th,td {
  font-size: 18px;
  color: #fff;
}

td {
  text-align: right;
}

table,
tr:hover{
  color: red;
}

.change_btn{
  background-image: linear-gradient(to right, cyan, magenta);
}

.change_btn:hover{
  transform: scale(0.9);
}

</style>
