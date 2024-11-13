<template>
    <div class="content" v-if="apiData">
      <div class="card-top">
        <div class="info">
          <h1>{{ apiData.name }}</h1>
          <p>{{ formattedDate }}</p>
          <p>{{ formattedTime }}</p>
        </div>
        <slot name="settings"></slot>
      </div>
      <div class="temp">
        <div class="left">
          <h2>{{ roundedTemperature }}°</h2>
          <img :src="'https://openweathermap.org/img/wn/' + apiData.weather[0].icon + '@2x.png'" alt="Weather Icon">
        </div>
        <div class="right">
          <h4>{{ apiData.weather[0].description }}</h4>
          <p>Feels like {{ roundedFeelsLike }}°</p>
        </div>
      </div>
      <div class="details">
        <div class="detail">
          <p><i class="bi bi-droplet"></i> Humidity</p>
          <h4>{{ apiData.main.humidity }}%</h4>
        </div>
        <div class="detail">
          <p><i class="bi bi-cloud-drizzle"></i> Precipitation</p>
          <h4>{{ allData.forecast.forecastday[0].hour[0].chance_of_rain }} %</h4>
        </div>
        <div class="detail">
          <p><i class="bi bi-wind"></i> Wind</p>
          <h4>{{ allData.current.wind_kph }} km/h</h4>
        </div>
        <div class="detail">
          <p><i class="bi bi-speedometer2"></i> AQI</p>
          <h4>{{ aqi }}</h4>
        </div>
      </div>
      <div class="aqi">
        <div class="aqi-info">
          <h4>AQI</h4>
          <div class="tooltip-container">
            <div class="tooltip-trigger">
              300
              <div class="info-icon"><i class="bi bi-info-lg"></i></div>
            </div>
            <div class="tooltip-text">300+ AQI is considered hazardous</div>
          </div>
        </div>
        <div class="aqi-bar">
          <div class="bar" :style="{ width: barWidth , backgroundColor: barColor}"></div>
        </div>
      </div>
      <div class="forecast">
        <div class="switch">
            <button :class="{ active: status === 'hour' }" @click="toggleStatus('hour')">Hourly Forecast</button>
            <button :class="{ active: status === 'week' }" @click="toggleStatus('week')">7-Day Forecast</button>
        </div>
        <HourlyForecast v-if="status === 'hour'" :hourlyData="allData.forecast.forecastday[0].hour" :temperatureUnit="temperatureUnit"/>
        <WeeklyForecast v-else :dailyData="allData.forecast.forecastday" :temperatureUnit="temperatureUnit"/>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, PropType, computed, ref } from 'vue'
  import HourlyForecast from './HourlyForecast.vue'
  import WeeklyForecast from './WeeklyForecast.vue'

  export default defineComponent({
    name: 'WeatherCard',
    components: {
      HourlyForecast,
      WeeklyForecast
    },
    props: {
      apiData: {
        type: Object as PropType<any>,
        required: true
      },
      formattedDate: {
        type: String,
        required: true
      },
      formattedTime: {
        type: String,
        required: true
      },
      temperatureUnit: {
        type: String as PropType<'C' | 'F'>,
        default: 'C',
        required: false
      },
      allData: {
        type: Object as PropType<any>,
        required: false
      },
      aqi: {
        type: Number as PropType<number>,
        required: false
      },
      status: {
        type: String as PropType<'hour' | 'week'>,
        required: false,
        default: 'hour' 
      }
    },
    computed: {
      barWidth() {
        const maxAqi = 300;
        return Math.min((this.aqi / maxAqi) * 100, 100) + '%';
      },
      barColor() {
        const widthPercentage = parseFloat(this.barWidth);
        if (widthPercentage <= 16.67) {  // Up to 50 AQI
          return '#52b788';
        } else if (widthPercentage <= 33.33) {  // Up to 100 AQI
          return '#ffdd00';
        } else if (widthPercentage <= 50) {  // Up to 150 AQI
          return 'orange';
        } else {  // Beyond 150 AQI
          return '#fd3749';
        }
      },
    },
    setup(props, { emit }) {
      const status = ref(props.status);
  
      const roundedTemperature = computed(() => {
        const temp = props.temperatureUnit === 'C' ? props.allData.current.temp_c : props.allData.current.temp_f;
        return temp % 1 >= 0.5 ? Math.ceil(temp) : Math.floor(temp);
      });
  
      const roundedFeelsLike = computed(() => {
        const feelsLike = props.temperatureUnit === 'C' ? props.allData.current.feelslike_c : props.allData.current.feelslike_f;
        return feelsLike % 1 >= 0.5 ? Math.ceil(feelsLike) : Math.floor(feelsLike);
      });
  
      const toggleStatus = (newStatus: 'hour' | 'week') => {
        status.value = newStatus;
        emit('update:status', newStatus);
      };
  
      return {
        roundedTemperature,
        roundedFeelsLike,
        status,
        toggleStatus
      }
    }
  })
  </script>
  
  <style scoped>
  .right h4 {
    text-transform: capitalize;
  }

  </style>
  