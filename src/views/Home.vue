<template>
    <div class="centered-content">
      <div class="weather-widget">
        <div class="widget-header">
          <h2 class="widget-title">INFORMASI CUACA</h2>
        </div>
        <q-input
          filled
          v-model="searchQuery"
          label="MASUKAN NAMA KOTA"
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
          error.value =
            "Gagal mengambil data cuaca atau kota tidak ditemukan";
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
    min-height: calc(100vh - 60px); /* Mengurangi tinggi navbar dari 100vh */
    margin-top: 90px; /* Jarak dari atas halaman */
  }
  
  .weather-widget {
    width: 1000px; /* Ubah nilai width sesuai dengan kebutuhan */
    padding: 40px;
    border-radius: 20px;
    background-color: #f0f0f032;
    box-shadow: 0px 0px 20px 0px rgba(0, 0, 0, 0.2);
    background-image: url("https://i.pinimg.com/736x/39/cc/b8/39ccb89d5fa58e1aa7cbeaa60be1f149.jpg"); /* Ganti dengan URL gambar latar belakang yang valid */
    background-size: cover;
    background-position: center;
    text-align: center; /* Memastikan konten di tengah secara vertikal */
  }
  
  .widget-header {
    text-align: center;
    margin-bottom: 15px;
  }
  
  .widget-title {
    font-size: 2.8rem;
    color: #20a46fc0;
    font-family:'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif
  }
  
  .location-input {
    width: calc(100% - 10px); /* Sesuaikan nilai width untuk menyesuaikan dengan tombol */
    padding: 12px;
    border: 1px solid #0a0a0a;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    margin: center;
    background-color: hwb(0 100% 0% / 0.34);
  }
  
  .search-button {
    width: 110px; /* Ubah nilai width sesuai dengan kebutuhan */
    padding: 12px 25px;
    background-color: #20a46fc0;
    color: #fff;
    border: none;
    border-radius: 7px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    cursor: pointer;
    transition: background-color 0.3s ease;
    margin-top: 8px;
  }
  
  .search-button:hover {
    background-color: #45a049;
  }
  
  .loading-message {
    font-style: italic;
    color: #121212;
    margin-top: 10px;
  }
  
  .weather-result {
    margin-top: 20px;
    padding: 15px;
    border-radius: 5px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
    transition: box-shadow 0.3s ease, transform 0.3s ease;
    background-color: hwb(0 100% 0% / 0.28);
  }
  
  .weather-result:hover {
    box-shadow: 0px 0px 15px rgba(0, 0, 0, 0.2);
    transform: translateY(-5px);
  }
  
  .weather-info {
    margin-bottom: 10px;
    text-align: center;
  }
  
  .city-name {
    font-weight: bold;
    color: #1b1919;
    font-size: 1.5rem;
  }
  
  .temperature {
    font-size: 3rem;
    color: #0e0606;
  }
  
  .weather-icon {
    width: 100px;
    height: 100px;
    margin: 0 auto;
    display: block;
  }
  
  .weather-description {
    font-style: italic;
    color: #080303;
  }
  
  .additional-info {
    margin-top: 10px;
  }
  
  .additional-info p {
    margin: 5px 0;
    color:#080303
  }
  
  .error-message {
    color: red;
    margin-top: 10px;
    text-align: center;
  }
  </style>
  