<template>
  <main :class="{ warm: isWarmWeather }">
    <div class="search-box">
      <input
        type="text"
        class="search-bar"
        placeholder="Search..."
        v-model="query"
        @keypress="fetchWeather"
      />
    </div>
    <div class="weather-wrap" v-if="weather">
      <div class="location-box">
        <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
        <div class="date">{{ dateBuilder() }}</div>
      </div>

      <div class="weather-box">
        <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
        <div class="weather">{{ weather.weather[0].main }}</div>
        <div class="details">
          <div class="humidity">Humidity: {{ weather.main.humidity }}%</div>
          <div class="wind-speed">Wind Speed: {{ weather.wind.speed }} m/s</div>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref, computed } from 'vue'

const api_key = 'f1d44e1029400d937bbbbc4c372d95c7'
// const api_key = import.meta.env.VUE_APP_API_KEY;
// console.log(api_key)
// .env not working to store api_key for best practice
const api_base_url = 'https://api.openweathermap.org/data/2.5/'

const query = ref('')
const weather = ref(null)
const temperature = ref(null)

const fetchWeather = (event) => {
  if (event.key === 'Enter') {
    fetch(`${api_base_url}weather?q=${query.value}&units=metric&appid=${api_key}`)
      .then((res) => res.json())
      .then((data) => {
        weather.value = data
        temperature.value = data.main.temp
        clearQuery()
      })
      .catch((error) => {
        console.error('Error fetching weather data:', error)
      })
  }
}

const dateBuilder = () => {
  let d = new Date()

  let months = [
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

  let days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']

  let day = days[d.getDay()]
  let date = d.getDate()
  let month = months[d.getMonth()]
  let year = d.getFullYear()

  return `${day} ${date} ${month}, ${year}`
}

const clearQuery = () => {
  query.value = ''
}

const isWarmWeather = computed(() => {
  return temperature.value && temperature.value > 16
})
</script>

<style scoped>
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  border-radius: 0 14px 0 14px;
  outline: none;
  background: none;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  transition: 0.4s;
  text-transform: capitalize;
}

.search-box .search-bar:focus {
  background-color: rgba(255, 255, 255, 0.75);
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  border-radius: 14px 0 14px 0;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .details {
  margin-top: 20px; 
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 20px;
  font-weight: 600;
  text-shadow: 3px 4px rgba(0, 0, 0, .2);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 10px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
 
}

.weather-box .wind-speed, .humidity {
  font-size: 16px;
  color: #fff;
  margin-top: 15px;
}

.warm {
  background-image: url('../assets/warm-bg.jpg');
}
</style>
