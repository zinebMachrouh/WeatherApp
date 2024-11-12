<template>
    <div id="bg">
      <div class="card" v-if="latitude && longitude">
        <WeatherCard
          :apiData="apiData"
          :formattedDate="formattedDate"
          :formattedTime="formattedTime"
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
        <Loader v-if="loading" />
        <h2 v-if="errorMessage" style="color: red;">{{ errorMessage }}</h2>
      </div>
    </div>
  </template>
  
  <script lang="ts">

  import { ref, onMounted } from 'vue'
  import axios from 'axios'
  import WeatherCard from './WeatherCard.vue'
  import SettingsPopup from './SettingsPopup.vue'
  import Loader from './Loader.vue'
  
  export default {
    components: {
      WeatherCard,
      SettingsPopup,
      Loader
    },
    setup() {
      const latitude = ref<number | null>(null)
      const longitude = ref<number | null>(null)
      const errorMessage = ref<string | null>(null)
      const API_KEY = "188f6e276831bbab19cc0ba7441ef581"
      const BASE_URL = "https://api.openweathermap.org/data/2.5/"
      const apiData = ref<any>(null)
      const loading = ref<boolean>(true)
      const formattedDate = ref<string>("")
      const formattedTime = ref<string>("")
  
      const isPopupVisible = ref<boolean>(false)
      const temperatureUnit = ref<string>("C")
      const measurementUnit = ref<string>("metric")
  
      const togglePopup = () => {
        isPopupVisible.value = !isPopupVisible.value
      }
      const setTemperatureUnit = (unit: string) => {
        temperatureUnit.value = unit
      }
      const setMeasurementUnit = (unit: string) => {
        measurementUnit.value = unit
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
                getCurrentDateTime()
                console.log("API Response:", apiData.value)
            } catch (error) {
                console.error("Error fetching API data:", error)
                errorMessage.value = 'Failed to fetch data from the API.'
            } finally {
                loading.value = false 
            }
        }
    }

  
      onMounted(() => {
        getLocation()
      })
  
      return {
        latitude,
        longitude,
        errorMessage,
        apiData,
        loading,
        formattedDate,
        formattedTime,
        isPopupVisible,
        temperatureUnit,
        measurementUnit,
        togglePopup,
        setTemperatureUnit,
        setMeasurementUnit
      }
    }
  }
  </script>
  
  <style scoped>

  </style>
