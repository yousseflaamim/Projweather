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
     
       
  <div class="hourly-forecast" v-if="weatherData.hourlyForecast.length > 0">
  <h2>Hourly Forecast</h2>
  <ul class="forecast-list">
    <li v-for="(forecast, index) in weatherData.hourlyForecast" :key="index" class="forecast-item">
      <div class="forecast-time">{{ forecast.time }}</div>
      <div class="forecast-temperature">{{ forecast.temperature }}°</div>
      <div class="forecast-description">{{ forecast.description }}</div>
      <div class="forecast-icon">
        <img :src="getWeatherIconUrl(forecast.icon)" :alt="forecast.description" />
      </div>
    </li>
  </ul>
</div>


    </div>
      <div class="copyright">
      &copy; 2023 Youssef Laamim. all rights are save.
    </div>
    
  </div>
</template>

<script>
/*
*Autore: Youssef Laim
*Diritto d'autore (c) 2023
*this code see weather for 3 next hours 
 */
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
            city: this.city, 
            icon: response.data.weather[0].icon,
            temperature: response.data.main.temp,
            description: response.data.weather[0].description,
            wind: response.data.wind.speed + ' km/h',
            date: new Date(response.data.dt * 1000).toLocaleDateString(),
            time: new Date(response.data.dt * 1000).toLocaleTimeString(),
          };

     
      localStorage.setItem('city', this.city);

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
        city: city, 
        icon: response.data.weather[0].icon,
        temperature: response.data.main.temp,
        description: response.data.weather[0].description,
        wind: response.data.wind.speed + ' km/h',
        date: new Date(response.data.dt * 1000).toLocaleDateString(),
        time: new Date(response.data.dt * 1000).toLocaleTimeString(),
        hourlyForecast: [] 

      };
     this.getHourlyForecast(city); // Get the weather for the next hoursاحصل على  الطقس ساعات قادمة
    })
    .catch(error => {
      console.error(error);
    });
},
//get the weather  for the coming hoursحصول على الطقس ساعات قادمة
getHourlyForecast(city) {
  axios
    .get(`https://api.openweathermap.org/data/2.5/forecast?q=${city}&appid=f8f3b74f08b893a86a9c7eb9ee44f98f&units=metric`)
    .then(response => {
      const forecastData = response.data.list.slice(0, 4).map((item, index) => {
        
        const hourOffset = (index + 1) * 1; 
        const forecastTime = new Date(Date.now() + hourOffset * 60 * 60 * 1000).toLocaleTimeString();
        
        return {
          time: forecastTime,
          temperature: item.main.temp,
          description: item.weather[0].description,
          icon: item.weather[0].icon
        };
      });

      this.weatherData.hourlyForecast = forecastData;
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
    // استعادة معلمة المدينة من back city from localStorage
    const savedCity = localStorage.getItem('city');
    if (savedCity) {
      this.city = savedCity;
      this.searchWeather();
    }

    
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
  background-color: rgba(255, 255, 255, 0.5); 
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
  justify-content: center; 
}

.city-list li {
  font-size: 18px;
  margin: 0 10px;
  
  

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
.forecast-list {
  display: flex;
  flex-direction: row;
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.forecast-item {
  width: 200px; 
 margin-right: 20px; 
}
.copyright {

  position: absolute;
  bottom: 10px; 
  left: 10px;
  font-size: 12px;
  color: rgb(10, 9, 9);
}


</style>
