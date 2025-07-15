<template>
  <div class="days-tab text-center">
    <div v-if="loading" class="loading">Loading...</div>
    <ul v-else class="p-0">
        <li v-for ="day in forecast" :key="day.date" class="li_active">
            <div class="py-3"><img :src="day.iconUrl"></div>
            <div class="py-3">{{ getDayName(day.date) }}</div>
            <div class="py-3">
      {{ day.temp_min }}&deg;C |  {{ day.temp_max }}&deg;C
    </div>
        </li>
        
    </ul>
  </div>
</template>

<script>

import axios from 'axios';
import moment from 'moment';
export default (await import('vue')).defineComponent({
  props:{
    cityname : String
  },
  data(){
    return{
        forecast: [],
        loading: true,
        iconUrl: null,
    }
  },
  mounted(){
    this.fetchWeatherData();
  },
  methods:{
    async fetchWeatherData(){
  const apikey = '4ce1eea15ac17ef2217657495f8e4622';
  const city = this.cityname;
  const apiUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apikey}`;

  try {
    const response = await axios.get(apiUrl);
    const forecastData = response.data.list;

    // groepeer items per dag (YYYY-MM-DD)
    const groupedByDay = forecastData.reduce((acc, item) => {
      const day = item.dt_txt.split(' ')[0]; // b.v. "2025-07-15"
      if (!acc[day]) acc[day] = [];
      acc[day].push(item);
      return acc;
    }, {});

    // Array met min en max temp per dag en icon van eerste item die dag
    const dailyTemps = Object.entries(groupedByDay).map(([date, items]) => {
      const temps = items.map(i => i.main.temp);
      const minTemp = Math.round(Math.min(...temps));
      const maxTemp = Math.round(Math.max(...temps));
      const icon = items[0].weather[0].icon;
      return {
        date: moment(date),
        temp_min: minTemp,
        temp_max: maxTemp,
        iconUrl: `https://openweathermap.org/img/w/${icon}.png`,
      };
    });

    this.forecast = dailyTemps.slice(1, 5);
    this.loading = false;

  } catch (error) {
    console.error('Error fetching weather data:', error);
    this.loading = false;
  }
},

    getDayName(date){
        return date.format('ddd')
    }
  }
})
</script>


<style scoped>
.days-tab{
    width: 90%;
    box-shadow: 0 4px 8px 0 rgb(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    border-radius: 2;
    width: 95%;
    margin: auto;
}

.loading{
    color: #fff;
}

ul{
    margin: 0;
}

li{
    display: inline-block;
    list-style: none;
    height: 100%;
    width: 21%;
    max-width: 21%;
    font-size: 1rem;
    line-height: 1.2;
}

span{
    display: block;
    margin-bottom: 5px;
    font: 100% sans-serif;
    height: 35px;
}

.li_active {
  background: rgba(37, 61, 92, 0.7); /* zacht blauw met transparantie */
  border-radius: 10px;
  margin: 0.5rem;
  color: #f5f5f5;
  font-weight: 600;
  display: inline-block;
  width: 21%;
  max-width: 21%;
  vertical-align: top;
  text-align: center;
  transition: background-color 0.3s ease;
}

.li_active:hover {
  background-color: rgba(37, 61, 92, 0.9); /* iets donkerder bij hover */
  transform: scale(1.1);
}


.li_active_temp{
    display: inline-block;
    background-color: #222831;
    color: #f5f5f5;
    transition: background-color 0.5s;
    border-radius: 10px;
}

@media (max-width: 1268px) {
  .li_active {
    width: 48%;
    max-width: 48%;
    font-size: 0.85rem;
    margin: 0.5rem 1%;
  }

  .li_active img {
    width: 35px;
    height: 35px;
    
  }

  .py-3 {
    padding-top: 0.17rem !important;
    padding-bottom: 0.2rem !important;
  }
}
</style>
