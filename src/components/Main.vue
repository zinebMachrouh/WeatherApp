<script lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

export default {
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

    // Fetch the user's location when the component is mounted
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
      formattedTime
    }
  }
}
</script>

<template>
  <div id="bg">
    <div class="card" v-if="latitude && longitude">
      <div class="content" v-if="apiData">
        <div class="card-top">
            <div class="info">
                <h1>{{ apiData.name }}</h1>
                <p>{{ formattedDate }}</p>
                <p>{{ formattedTime }}</p>
            </div>
            <div class="settings"></div>
        </div>
      </div>
      <!-- This loader will be displayed while the data is being fetched -->
      <div class="loader" v-if="loading"></div>

      <!-- Error Message -->
      <h2 v-if="errorMessage" style="color: red;">{{ errorMessage }}</h2>
    </div>
  </div>
</template>

<style scoped>
.loader {
  width: 45px;
  aspect-ratio: 1;
  --c: no-repeat linear-gradient(#cccccc 0 0);
  background: 
    var(--c) 0%   50%,
    var(--c) 50%  50%,
    var(--c) 100% 50%;
  background-size: 20% 100%;
  animation: l1 1s infinite linear;
}
@keyframes l1 {
  0%  {background-size: 20% 100%,20% 100%,20% 100%}
  33% {background-size: 20% 10% ,20% 100%,20% 100%}
  50% {background-size: 20% 100%,20% 10% ,20% 100%}
  66% {background-size: 20% 100%,20% 100%,20% 10% }
  100%{background-size: 20% 100%,20% 100%,20% 100%}
}
</style>
