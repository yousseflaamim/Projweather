<template>
  <div id="app">
    <h1>APP WEATHER </h1>
    <div class="city-list">
      <h2> citys</h2>
      <ul>
        <li v-for="(city, index) in savedCities" :key="index" @click="getWeatherInfo(city)">
          {{ city }}
        </li>
      </ul>
    </div>
    <div class="city-search">
      <input v-model="city" type="text" placeholder="name city" />
      <button @click="searchWeather">search</button>
    </div>
    <div class="weather-info" v-if="weatherData">
      <div class="weather-icon">
        <img :src="getWeatherIconUrl(weatherData.icon)" :alt="weatherData.description" />
      </div>
      <div class="city">{{ weatherData.city }}</div>
       <div class="date">{{ weatherData.date }}</div>
      <div class="time">{{ weatherData.time }}</div>
      <div class="temperature">{{ weatherData.temperature }}°</div>
      <div class="weather-description">{{ weatherData.description }}</div>
      <div class="wind-status">{{ weatherData.wind }}</div>
    </div>
    <div class="weather-info" v-if="weatherData && weatherData.city !== city">
      <div class="city">{{ weatherData.city }}</div>
      <div class="weather-icon">{{ weatherData.icon }}</div>
      <div class="temperature">{{ weatherData.temperature }}°</div>
      <div class="weather-description">{{ weatherData.description }}</div>
      <div class="wind-status">{{ weatherData.wind }}</div>
      <div class="date">{{ weatherData.date }}</div>
      <div class="time">{{ weatherData.time }}</div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      city: '',
      weatherData: null,
      savedCities: [],
      
    };
  },
  methods: {
    searchWeather() {
      axios
        .get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=f8f3b74f08b893a86a9c7eb9ee44f98f&units=metric`)
        .then(response => {
          this.weatherData = {
            city: this.city, // تحديث المدينة المحددةUpdate the selected city
            icon: response.data.weather[0].icon,
            temperature: response.data.main.temp,
            description: response.data.weather[0].description,
            wind: response.data.wind.speed + ' km/h',
            date: new Date(response.data.dt * 1000).toLocaleDateString(),
            time: new Date(response.data.dt * 1000).toLocaleTimeString(),
          };

      // حفظ معلمة المدينة في localStorage
      localStorage.setItem('city', this.city);

      // تحديث قائمة المدن المحفوظةUpdate the list of saved cities
      this.updateSavedCities();
    })
    .catch(error => {
      console.error(error);
    });
},
updateSavedCities() {
  if (!this.savedCities.includes(this.city)) {
    this.savedCities.push(this.city);
    localStorage.setItem('savedCities', JSON.stringify(this.savedCities));
  }
},
getWeatherInfo(city) {
  axios
    .get(`https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=f8f3b74f08b893a86a9c7eb9ee44f98f&units=metric`)
    .then(response => {
      this.weatherData = {
        city: city, // اسم المدينة المحددةname specified city
        icon: response.data.weather[0].icon,
        temperature: response.data.main.temp,
        description: response.data.weather[0].description,
        wind: response.data.wind.speed + ' km/h',
        date: new Date(response.data.dt * 1000).toLocaleDateString(),
        time: new Date(response.data.dt * 1000).toLocaleTimeString(),
      };
    })
    .catch(error => {
      console.error(error);
    });
},


    
    getWeatherIconUrl(iconCode) {
      return `https://openweathermap.org/img/w/${iconCode}.png`;
    },
  },
  mounted() {
    // استعادة معلمة المدينة من localStorage
    const savedCity = localStorage.getItem('city');
    if (savedCity) {
      this.city = savedCity;
      this.searchWeather();
    }

    // تحديث قائمة المدن المحفوظةUpdate the list of saved cities
    this.updateSavedCities();
  },
};
</script>

<style>
#app {
font-size: 15px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  text-align: center;
   background-image: url("../assets/foto3.jpg");
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  background-color: rgba(255, 255, 255, 0.5); /* شفافية الخلفية background transparency*/
}
.city-list {
  margin-bottom: 20px;
}

.city-list h2 {
  font-size: 24px;
}

.city-list ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
  display: flex;
  justify-content: center; /* Center the elements horizontally توسيط العناصر أفقيًا */
}

.city-list li {
  font-size: 18px;
  margin: 0 10px;
  
  
   /* ضبط التباعد بين الأسماء /* set spacing between names */
}

.city-search {
  margin-bottom: 20px;
}

.weather-info {
  margin-bottom: 20px;
}
.city{
    font-size: 20px;
    font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
}

</style>
