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
      <div v-if="allData">
        <h4>salam</h4>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, PropType, computed } from 'vue'
  
  export default defineComponent({
    name: 'WeatherCard',
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
        type: String,
        required: true
      },
      allData: {
        type: Object as PropType<any>,
        required: false
      }
    },
    setup(props) {
        const roundedTemperature = computed(() => {
            const temp = props.temperatureUnit === 'C' ? props.allData.current.temp_c : props.allData.current.temp_f;
            return temp % 1 >= 0.5 ? Math.ceil(temp) : Math.floor(temp);
        });

        const roundedFeelsLike = computed(() => {
            const feelsLike = props.temperatureUnit === 'C' ? props.allData.current.feelslike_c : props.allData.current.feelslike_f;
            return feelsLike % 1 >= 0.5 ? Math.ceil(feelsLike) : Math.floor(feelsLike);
        });

      return {
        roundedTemperature,
        roundedFeelsLike,
      }
    }
  })
  </script>
  
  <style scoped>
  .right h4 {
    text-transform: capitalize;
  }
  </style>
  