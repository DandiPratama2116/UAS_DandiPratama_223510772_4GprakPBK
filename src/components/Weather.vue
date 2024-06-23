<template>
    <div class="centered-content">
      <div class="weather-widget">
        <h2 class="widget-title">Cuaca</h2>
        <q-input
          filled
          v-model="searchQuery"
          label="Masukkan Nama Kota"
          class="location-input"
        />
        <q-btn @click="searchWeather" class="search-button">Cari</q-btn>
        <div v-if="loading" class="loading-message">Loading data...</div>
        <div v-else-if="weatherData" class="weather-result">
          <div class="weather-info">
            <p class="city-name">{{ weatherData.name }}</p>
            <p class="temperature">{{ weatherData.main.temp }}°C</p>
            <img
              :src="getWeatherIconUrl(weatherData.weather[0].icon)"
              :alt="weatherData.weather[0].description"
              class="weather-icon"
            />
            <p class="weather-description">
              {{ weatherData.weather[0].description }}
            </p>
          </div>
          <div class="additional-info">
            <p>Kelembaban: {{ weatherData.main.humidity }}%</p>
            <p>Kecepatan Angin: {{ weatherData.wind.speed }} m/s</p>
          </div>
        </div>
        <div v-else-if="error" class="error-message">{{ error }}</div>
      </div>
    </div>
  </template>
  
  <script>
  import { ref } from "vue";
  import axios from "axios";
  import { QInput, QBtn } from "quasar";
  
  export default {
    components: { QInput, QBtn },
    setup() {
      const searchQuery = ref("");
      const weatherData = ref(null);
      const loading = ref(false);
      const error = ref(null);
  
      const searchWeather = async () => {
        loading.value = true;
        error.value = null;
  
        try {
          const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?q=${searchQuery.value}&appid=bcbc6cc1860d02bd1f4306314c08d7e0&units=metric`
          );
          if (response.data.cod !== 200) {
            throw new Error("City not found");
          }
          weatherData.value = response.data;
        } catch (error) {
          console.error(error);
          error.value = "Gagal mengambil data cuaca atau kota tidak ditemukan";
        } finally {
          loading.value = false;
        }
      };
  
      const getWeatherIconUrl = (iconCode) => {
        return `https://openweathermap.org/img/wn/${iconCode}.png`;
      };
  
      return {
        searchQuery,
        weatherData,
        loading,
        error,
        searchWeather,
        getWeatherIconUrl,
      };
    },
  };
  </script>
  
  <style scoped>
  .centered-content {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background: linear-gradient(135deg, #f5f7fa, #c3cfe2);
    padding: 20px;
  }
  
  .weather-widget {
    max-width: 400px;
    width: 100%;
    padding: 20px;
    border-radius: 15px;
    background-color: rgba(255, 255, 255, 0.8);
    box-shadow: 0px 4px 20px rgba(0, 0, 0, 0.1);
    backdrop-filter: blur(10px);
    text-align: center;
  }
  
  .widget-title {
    font-size: 2rem;
    margin-bottom: 20px;
    color: #333;
  }
  
  .location-input {
    width: 100%;
    margin-bottom: 20px;
  }
  
  .search-button {
    width: 100%;
    padding: 12px;
    background-color: #20a46fc0;
    color: #fff;
    border: none;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  .search-button:hover {
    background-color: #20a46fc0;
  }
  
  .loading-message {
    font-style: italic;
    color: #888;
    margin-top: 10px;
  }
  
  .weather-result {
    margin-top: 20px;
    padding: 15px;
    border-radius: 10px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    background-color: #fff;
    animation: fadeIn 0.5s ease-in-out;
  }
  
  .weather-info {
    margin-bottom: 10px;
  }
  
  .city-name {
    font-weight: bold;
    color: #333;
    font-size: 1.5rem;
  }
  
  .temperature {
    font-size: 3rem;
    color: #20a46fc0;
  }
  
  .weather-icon {
    width: 80px;
    height: 80px;
  }
  
  .weather-description {
    font-style: italic;
    color: #666;
  }
  
  .additional-info {
    margin-top: 10px;
    text-align: left;
  }
  
  .additional-info p {
    margin: 5px 0;
    color: #333;
  }
  
  .error-message {
    color: red;
    margin-top: 10px;
    text-align: center;
  }
  
  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
  </style>
