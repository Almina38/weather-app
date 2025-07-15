<template>
  <div class="container p-0">
    <div class="d-flex">
      <div :style="backgroundStyle" class="card main-div w-100">
        <div class="p-3">
          <h2 class="today">Today</h2>
          <p class="text light date mb-0">{{ date }}</p>
          <small>{{ time }}</small>
          <h2 class="place">
            <i class="fa fa-location"></i> {{ name }} <small>{{ country }}</small>
          </h2>
          <div class="temp">
            <h1 class="weather-temp">{{ temperature }}&deg;</h1>
            <h2 class="text-light">
              {{ description }} <img :src="iconUrl" />
            </h2>
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
              <td>{{ windKmh }}</td>
            </tr>
          </tbody>
        </table>

        <DaysWeather :cityname="cityname" />

        <div id="div_Form" class="d-flex m-3 justify-content-center">
          <form action="">
            <input
              type="button"
              value="Change Location"
              @click="changeLocation"
              class="btn change_btn btn-primary"
            />
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import DaysWeather from './DaysWeather.vue'

export default {
  name: 'myWeather',
  components: {
    DaysWeather
  },
  props: {
    city: String
  },
  data() {
    return {
      cityname: this.city,
      temperature: null,
      description: null,
      iconUrl: null,
      date: null,
      time: null,
      name: null,
      temp_min: null,
      temp_max: null,
      wind: null,
      humidity: null,
      country: null,
      monthNames: [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December'
      ]
    }
  },
  methods: {
    changeLocation() {
      window.location.reload()
    }
  },
  computed: {
    windKmh() {
    if (!this.wind) return '';
    return Math.round(this.wind * 3.6) + ' km/h';
  },
    backgroundStyle() {
      if (!this.iconUrl) return {}

      const match = this.iconUrl.match(/\/(\d{2}[dn])@/)
      const iconCode = match ? match[1] : '01d'

      const bgMap = {
        '01d': 'sunny.jpg',
        '01n': 'clear-night.jpg',
        '02d': 'few-clouds-day.jpg',
        '02n': 'few-clouds-night.jpg',
        '03d': 'scattered-clouds.jpg',
        '03n': 'scattered-clouds-night.jpg',
        '04d': 'broken-clouds.jpg',
        '04n': 'broken-clouds-night.jpg',
        '09d': 'shower-rain.jpg',
        '09n': 'shower-rain-night.jpg',
        '10d': 'rain.jpg',
        '10n': 'rain-night.jpg',
        '11d': 'thunderstorm.jpg',
        '11n': 'thunderstorm-night.jpg',
        '13d': 'snow.jpg',
        '13n': 'snow-night.jpg',
        '50d': 'mist.jpg',
        '50n': 'mist-night.jpg'
      }

      const imageFile = bgMap[iconCode] || 'default.jpg'

      return {
        backgroundImage: `url('/img/${imageFile}')`,
        backgroundSize: 'cover',
        backgroundPosition: 'center',
        backgroundBlendMode: 'overlay',
        backgroundColor: 'rgba(0,0,0,0.5)',
        backgroundRepeat: 'no-repeat',
        borderRadius: '20px',
        color: '#fff',
        marginLeft: '0px'
      }
    }
  },
  async created() {
    const response = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=4ce1eea15ac17ef2217657495f8e4622`
    )
    const weatherData = response.data
    this.temperature = Math.round(weatherData.main.temp)
    this.description = weatherData.weather[0].description
    this.name = weatherData.name
    this.wind = weatherData.wind.speed
    this.country = weatherData.sys.country
    this.humidity = weatherData.main.humidity
    this.temp_min = Math.round(weatherData.main.temp_min)
    this.temp_max = Math.round(weatherData.main.temp_max)
    this.iconUrl = `https://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`

    const utcNow = Math.floor(Date.now() / 1000);
const localTimestamp = utcNow + weatherData.timezone; // offset in seconden

const localDate = new Date(localTimestamp * 1000);

this.date =
  localDate.getUTCDate() +
  ' ' +
  this.monthNames[localDate.getUTCMonth()] +
  ' ' +
  localDate.getUTCFullYear();

this.time =
  localDate.getUTCHours().toString().padStart(2, '0') +
  ':' +
  localDate.getUTCMinutes().toString().padStart(2, '0');

  }
}
</script>

<style scoped>
body {
  background-color: #343d4b;
  
}

.weather-temp {
  margin: 0;
  font-weight: 700;
  font-size: 4em;
}
.container{
  max-width: 80%;
}
h2.today {
  font-size: 3rem;
  font-weight: 400;
}

.temp {
  position: absolute;
  bottom: 0;
}

.main-div {
  flex-basis: 55%;
  max-width: 55%;
}

.card-2 {
  background-color: #212730;
  border-radius: 20px;
  max-height: 480px;
  flex-basis: 45%;
  max-width: 45%;
}


.card-details {
  margin-left: 19px;
}

table {
  position: relative;
  border-collapse: separate;
  border-spacing: 15px;
  width: 80%;
  text-align: left;
  max-width: 600px;
  margin: 0 auto;
}

th,
td {
  font-size: 18px;
  color: #fff;
}

td {
  text-align: right;
}

table,
tr:hover {
  color: red;
}

.change_btn {
  background-image: linear-gradient(to right, cyan, magenta);
}

.change_btn:hover {
  transform: scale(0.9);
}
</style>
