<template>
  <div class="container p-0">
    <div v-if="errorMessage" class="alert alert-danger text-center mt-3">
      {{ errorMessage }}
    </div>

    <div v-else class="d-flex">
      <div :style="backgroundStyle" class="card main-div w-100">
        <div class="p-3">
          <div class="today-date-wrapper">
            <h2 class="today">Today</h2>
            <p class="text light date mb-0">{{ date }}</p>
          </div>
          <small class="time">{{ time }}</small>
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

      <div class="card card-2 w-100" v-if="!errorMessage">
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

        <div id="div_Form" class="m-3">
          <form class="d-flex justify-content-center" action="">
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
  components: { DaysWeather },
  props: { city: String },
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
      errorMessage: '',
      monthNames: [
        'January', 'February', 'March', 'April', 'May', 'June',
        'July', 'August', 'September', 'October', 'November', 'December'
      ]
    }
  },
  methods: {
    changeLocation() {
      window.location.reload()
    },
    isToday(dateStr) {
      const today = new Date()
      const date = new Date(dateStr)
      return date.getDate() === today.getDate() &&
             date.getMonth() === today.getMonth() &&
             date.getFullYear() === today.getFullYear()
    }
  },
  computed: {
    windKmh() {
      if (!this.wind) return ''
      return Math.round(this.wind * 3.6) + ' km/h'
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
        color: '#f5f5f5',
        marginLeft: '0px'
      }
    }
  },
  async created() {
    try {
      // huidige weer ophalen
      const currentResponse = await axios.get(
        `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=4ce1eea15ac17ef2217657495f8e4622`
      )
      const currentData = currentResponse.data
      this.temperature = Math.round(currentData.main.temp)
      this.description = currentData.weather[0].description
      this.name = currentData.name
      this.wind = currentData.wind.speed
      this.country = currentData.sys.country
      this.humidity = currentData.main.humidity
      this.iconUrl = `https://openweathermap.org/img/wn/${currentData.weather[0].icon}@2x.png`

      const utcNow = Math.floor(Date.now() / 1000)
      const localTimestamp = utcNow + currentData.timezone
      const localDate = new Date(localTimestamp * 1000)

      this.date =
        localDate.getUTCDate() +
        ' ' +
        this.monthNames[localDate.getUTCMonth()] +
        ' ' +
        localDate.getUTCFullYear()

      this.time =
        localDate.getUTCHours().toString().padStart(2, '0') +
        ':' +
        localDate.getUTCMinutes().toString().padStart(2, '0')

      const forecastResponse = await axios.get(
        `https://api.openweathermap.org/data/2.5/forecast?q=${this.city}&units=metric&appid=4ce1eea15ac17ef2217657495f8e4622`
      )
      const forecastData = forecastResponse.data.list

      // Filter forecast voor vandaag
      const todayForecasts = forecastData.filter(item =>
        this.isToday(item.dt_txt)
      )

      if (todayForecasts.length > 0) {
        const tempsMin = todayForecasts.map(f => f.main.temp_min)
        const tempsMax = todayForecasts.map(f => f.main.temp_max)
        this.temp_min = Math.round(Math.min(...tempsMin))
        this.temp_max = Math.round(Math.max(...tempsMax))
      } else {
        // fallback naar huidige min/max als geen forecast vandaag beschikbaar is
        this.temp_min = Math.round(currentData.main.temp_min)
        this.temp_max = Math.round(currentData.main.temp_max)
      }

      this.errorMessage = ''
    } catch (error) {
      this.errorMessage = 'Please type the correct city name.'
      this.temperature = null
      this.description = null
      this.name = null
      this.wind = null
      this.country = null
      this.humidity = null
      this.temp_min = null
      this.temp_max = null
      this.iconUrl = null
      this.date = null
      this.time = null
    }
  }
}
</script>

<style scoped>
/* Jouw bestaande styles */
.weather-temp {
  margin: 0;
  font-weight: 700;
  font-size: 4em;
}

.time {
  font-size: 1.5rem;
  font-weight: 600;
}

.date {
  font-size: 1.1rem;
}

.container {
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
  backdrop-filter: blur(5px);
}

.card-2 {
  background: rgba(42, 42, 64, 0.85);
  backdrop-filter: blur(5px);
  border-radius: 20px;
  max-height: 600px;
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
  text-align: left;
  max-width: 600px;
  margin: 0 auto;
}

.place i.fa-location {
  font-size: 1rem;
  vertical-align: middle;
  margin-right: 0.3rem;
}

table th,
table td {
  font-size: 16px;
  color: #f5f5f5;
}

td {
  text-align: right;
}

.change_btn {
  background-color: #4fd1c5;
  color: #1e1e2f;
  border: none;
  border-radius: 8px;
  font-weight: 600;
}

.change_btn:hover {
  transform: scale(0.98);
  background-color: #38b2ac;
}

.today-date-wrapper {
  display: block;
}
.alert {
  background-color: #f44336;
  color: white;
  padding: 10px;
  border-radius: 5px;
  margin-bottom: 1rem;
}

@media (max-width: 768px) {
  .d-flex {
    flex-direction: column !important;
  }

  #div_Form form {
    justify-content: center;
    display: flex;
  }
  #div_Form input.change_btn {
    font-size: 0.9rem;
    padding: 6px 12px;
    width: auto;
    min-width: 120px;
    justify-content: center;
    margin-left: 0;
    margin-right: 0;
  }

  .today-date-wrapper {
    display: flex;
    gap: 0.5rem;
    align-items: center;
  }
  table {
    border-spacing: 0px 0px;
  }
  table th,
  table td {
    font-size: 15px;
    padding-left: 0;
    padding-right: 0;
  }
  table th{
    font-size: 13px;
  }

  table tbody {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 0.5rem 1rem;
  }

  table tbody tr {
    display: contents;
  }

  table tbody th {
    grid-row: 1;
    text-align: center;
    font-weight: 600;
    padding: 0;
  }

  table tbody td {
    grid-row: 2;
    text-align: center;
    padding: 0;
  }

  .main-div,
  .card-2 {
    max-width: 100% !important;
    flex-basis: 100% !important;
    max-height: none;
    min-height: 240px;
  }

  .card-2 {
    margin-bottom: 1rem;
  }

  .weather-temp {
    font-size: 2rem;
  }

  h2.today {
    font-size: 1.6rem;
    margin-bottom: 0;
    display: inline-block;
    vertical-align: middle;
  }

  .date {
    font-size: 0.9rem;
    display: inline-block;
    margin-left: 0.1rem;
    vertical-align: middle;
    position: relative;
    top: 8px;
  }

  .p-3 {
    margin-left: 20px;
  }

  .time {
    font-size: 1.1rem;
    margin-bottom: 0.5rem;
    margin-left: 2px;
    display: block;
  }

  .m-4 {
    font-size: 0.4rem;
  }

  .place {
    font-size: 1.1rem;
  }

  .temp h2 {
    font-size: 1rem;
  }

  .temp h2 img {
    width: 40px;
    height: 40px;
  }

  .p-3 {
    padding: 0.5rem !important;
  }
}
</style>
