<template>
  <div class="page-center">
    <div class="weather-container">
      <h1>Aplikasi Cuaca Vuejs</h1>

    <!--FORM PENCARIAN-->
    <div class="search-box">
      <input 
      v-model="city"
      @keyup.enter="fetchWeather"
      type="text"
      placeholder="Masukkan nama kota (Cth: Jakarta)"
      />
      <button @click="fetchWeather" :disabled="loading">Cari</button>
    </div>

    <!--TAMPILAN LOADING-->
    <p v-if="loading" class="loading">Sedang memuat data..</p>

    <!--TAMPILAN ERROR-->
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

    <!--TAMPILAN HASIL CUACA-->
    <div v-if="weatherData" class="weather-info">
      <h2>{{ weatherData.name }}, {{ weatherData.sys.country }}</h2>
      <div class="temp">{{ Math.round(weatherData.main.temp) }}°C</div>
      <p class="description">{{ weatherData.weather[0].description }}</p>

      <div class="details">

        <div class="detail-item">
          <span>Kelembapan: </span>
          <strong>{{ weatherData.main.humidity }}%</strong>
        </div>
        <div class="detail-item">
          <span>Kecepatan angin: </span>
          <strong>{{ weatherData.wind.speed }}m/s</strong>
        </div>
      </div>
    </div>
    </div>
  </div>
</template>


<script setup>
  import { ref } from 'vue'

  //state variabel reactive
  const city = ref('')
  const weatherData = ref(null)
  const loading = ref(false)
  const errorMessage = ref('')

  //API key
  const apiKey = 'cd1c48cc691a1b950eea1e73d84ade27'

  //Mengambil data cuaca
  const fetchWeather = async () => {
    if(!city.value.trim()) {
      errorMessage.value = 'Silahkan masukkan nama kota terlebih dahulu.'
      return
    }

    loading.value = true
    errorMessage.value = ''
    weatherData.value = null

    try {
      const response = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&appid=${apiKey}&lang=id`
      )

      if(!response.ok) {
        throw new Error('Kota tidak ditemukan. Silahkan coba lagi.')
      }

      const data = await response.json()
      weatherData.value = data
    } catch (error) {
      errorMessage.value = error.message
    } finally {
      loading.value = false
    }
  }
</script>

<style scoped>
.page-center {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 24px;
}

.weather-container {
  max-width: 400px;
  margin: 0;
  padding: 20px;

  border-radius: 12px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  background-color: #ffffff;
  font-family: Arial, sans-serif;
  text-align: center;
  color: #333;
}

h1 {
  margin-bottom: 20px;
  color: #007BFF;
  font-size: 1.5rem;
}

.search-box {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
  justify-content: center;
}

input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 1rem;
}

button {
  padding: 10px 15px;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
}

button:disabled {
  background-color: #a3e2c4;
  cursor: not-allowed;
}

.loading { color: #666; }
.error { color: #ff4d4d; font-weight: bold; }

.weather-info {
  margin-top: 20px;
  animation: fadeIn 0.5s ease;
}

.temp {
  font-size: 3.5rem;
  font-weight: bold;
  margin: 10px 0;
  color: #2c3e50;
}

.description {
  text-transform: capitalize;
  font-style: italic;
  color: #555;
}

.details {
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
  padding-top: 15px;
  border-top: 1px solid #eee;
}

.detail-item {
  display: flex;
  flex-direction: column;
  font-size: 0.9rem;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>