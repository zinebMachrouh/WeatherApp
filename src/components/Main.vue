<template>
<div id="bg">
    <div class="card" v-if="latitude && longitude || aqi">
    <WeatherCard
        :apiData="apiData"
        :formattedDate="formattedDate"
        :formattedTime="formattedTime"
        :temperatureUnit="temperatureUnit"
        :allData="allData"
        :aqi="aqi"
        >
        <template #settings>
        <div class="settings">
            <button @click="togglePopup" class="bi bi-gear-fill gear"></button>
            <SettingsPopup
            v-if="isPopupVisible"
            :isPopupVisible="isPopupVisible"
            :temperatureUnit="temperatureUnit"
            :measurementUnit="measurementUnit"
            @update:temperatureUnit="setTemperatureUnit"
            @update:measurementUnit="setMeasurementUnit"
            />
        </div>
        </template>
    </WeatherCard>
</div>
<Loader v-if="loading" />

    <div v-if="errorMessage" class="error-message">
        <h2>{{ errorMessage }}</h2>
        <div class="city-input">
            <input
                id="city"
                v-model="city"
                placeholder="Enter city name"
                @keyup.enter="fetchWeatherByCity"
                />
            <button @click="fetchWeatherByCity"><i class="bi bi-search"></i></button>
        </div>
    </div>
</div>
</template>

<script lang="ts">

import { ref, onMounted, onUnmounted } from 'vue'
import axios from 'axios'
import WeatherCard from './WeatherCard.vue'
import SettingsPopup from './SettingsPopup.vue'
import Loader from './Loader.vue'

// @ts-ignore
export default {
components: {
    WeatherCard,
    SettingsPopup,
    Loader
},
setup() {
    const latitude = ref<number | null>(null)
    const longitude = ref<number | null>(null)
    const city = ref<string>("")
    const errorMessage = ref<string | null>(null)
    const API_KEY = "188f6e276831bbab19cc0ba7441ef581"
    const API_KEY_ALL = "402fbfadc87441159fe141236241704"
    const BASE_URL = "https://api.openweathermap.org/data/2.5/"
    const BASE_URL_ALL = "https://api.weatherapi.com/v1/forecast.json?lang=4&days=7"
    const apiData = ref<any>(null)
    const allData = ref<any>(null)
    const loading = ref<boolean>(true)
    const formattedDate = ref<string>("")
    const formattedTime = ref<string>("")

    const isPopupVisible = ref<boolean>(false)
    const temperatureUnit = ref<'C' | 'F'>('C')
    const measurementUnit = ref<'metric' | 'imperial'>('metric')


    const aqi = ref<number | null>(null)

    const togglePopup = () => {
    isPopupVisible.value = !isPopupVisible.value
    }
    const setTemperatureUnit = (unit: string) => {
        temperatureUnit.value = unit as 'C' | 'F';  // Assert that unit is of type 'C' or 'F'
    }   

    const setMeasurementUnit = (unit: string) => {
        measurementUnit.value = unit as 'metric' | 'imperial';  // Assert that unit is 'metric' or 'imperial'
        fetchApiDataAll();
    }

    // This function fetches the user's location from the browser and updates the latitude and longitude refs
    const getLocation = () => {
        if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
            (position) => {
            latitude.value = position.coords.latitude
            longitude.value = position.coords.longitude
            errorMessage.value = null

            // Fetch data after getting the location
            fetchApiData()
            fetchApiDataAll()
            fetchAQIData()
            },
            (error) => {
            if (error.code === error.PERMISSION_DENIED) {
                errorMessage.value = 'Permission to access location was denied.'
            } else {
                errorMessage.value = 'An error occurred while fetching the location.'
            }
            loading.value = false 
            }
        )
        } else {
        errorMessage.value = 'Geolocation is not supported by this browser.'
        loading.value = false 
        }
    }

// This function gets the current date and time
const getCurrentDateTime = () => {
    const now = new Date()

    formattedDate.value = now.toLocaleDateString('en-US', {
        weekday: 'long', 
        year: 'numeric',
        month: 'long', 
        day: 'numeric'   
    })

    formattedTime.value = now.toLocaleTimeString('en-US', {
        hour: '2-digit', 
        minute: '2-digit', 
        hour12: true 
    })
}

// This function fetches the weather data from the OpenWeatherMap API using the latitude and longitude
const fetchApiData = async () => {
    if (latitude.value && longitude.value) {
        try {
            const response = await axios.get(
            `${BASE_URL}weather?lat=${latitude.value}&lon=${longitude.value}&appid=${API_KEY}&units=metric`
            )
            apiData.value = response.data
            console.log("API Response:", apiData.value)
        } catch (error) {
            console.error("Error fetching API data:", error)
            errorMessage.value = 'Failed to fetch data from the API.'
        } finally {
            loading.value = false 
        }
    }
}

// This function fetches the weather data from the WeatherAPI.com API using the latitude and longitude
const fetchApiDataAll = async () => {
    if (latitude.value && longitude.value) {
        try {
            const response = await axios.get(
            `${BASE_URL_ALL}&key=${API_KEY_ALL}&q=${latitude.value},${longitude.value}&units=${measurementUnit.value}`
            )
            allData.value = response.data
            console.log("API all Response:", allData.value)
        } catch (error) {
            console.error("Error fetching API all data:", error)
            errorMessage.value = 'Failed to fetch data from the API.'
        } finally {
            loading.value = false 
        }
    }
}

const fetchAQIData = async () => {
    if (latitude.value && longitude.value) {
        try {
            const response = await axios.get(
            `${BASE_URL}air_pollution?lat=${latitude.value}&lon=${longitude.value}&appid=${API_KEY}`
            )
            // @ts-ignore
            aqi.value = response.data.list[0].main.aqi
            console.log("AQI Response:", aqi.value)
        } catch (error) {
            console.error("Error fetching AQI data:", error)
            errorMessage.value = 'Failed to fetch data from the API.'
        } finally {
            loading.value = false 
        }
    }
}

const fetchWeatherByCity = async () => {
    if (!city.value) return errorMessage.value = 'Please enter a city name.';

    try {
        const [weatherResponse, allDataResponse] = await Promise.all([
            axios.get(`${BASE_URL}weather?q=${city.value}&appid=${API_KEY}&units=metric`),
            axios.get(`${BASE_URL_ALL}&key=${API_KEY_ALL}&q=${city.value}&units=${measurementUnit.value}`)
        ]);

        apiData.value = weatherResponse.data;

        allData.value = allDataResponse.data;

        if (apiData.value?.coord) {
            const { lat, lon } = apiData.value.coord;
            const aqiResponse = await axios.get(
                `${BASE_URL}air_pollution?lat=${lat}&lon=${lon}&appid=${API_KEY}`
            );

            // @ts-ignore
            aqi.value = aqiResponse.data.list[0].main.aqi;
        }

        errorMessage.value = null;

    } catch (error) {
        errorMessage.value = 'Failed to fetch data from the API for the specified city.';
        console.error(error);  
    } finally {
        loading.value = false;
    }
};

onMounted(() => {
    getLocation()
    getCurrentDateTime();  
    const intervalId = setInterval(getCurrentDateTime, 60000);
    
    onUnmounted(() => {
        clearInterval(intervalId);
    });
})

return {
    latitude,
    longitude,
    city,
    errorMessage,
    apiData,
    allData,
    loading,
    formattedDate,
    formattedTime,
    isPopupVisible,
    temperatureUnit,
    measurementUnit,
    togglePopup,
    setTemperatureUnit,
    setMeasurementUnit,
    aqi,
    fetchWeatherByCity
}
}
}
</script>

<style scoped>
.error-message {
  background-color: var(--white);
  color: var(--black);
  border-radius: 24px;
  width: 400px;
  padding: 24px 32px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.error-message h2 {
  font-size: 1.5rem;
}
.city-input {
  width: 100%;
  margin-top: 20px;
  display: flex;
  align-items: center;
  gap: 15px;
}

.city-input input {
  width: 300px;
  max-width: 300px;
  padding: 12px 16px;
  font-size: 1rem;
  border-radius: 12px;
  border: 1px solid var(--black);
  background-color: var(--white);
  color: var(--black);
  transition: border-color 0.3s ease;
}

.city-input input:focus {
  border-color: var(--blue);
  outline: none;
}

.city-input button {
  padding: 12px 0px;
  background-color: var(--blue);
  color: var(--white);
  font-size: 1rem;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  width: 50px;
  max-width: 50px;
}

.city-input button:hover {
  background-color: #3B61F8;
  transition: all 0.3s ease-in-out;
}

</style>
