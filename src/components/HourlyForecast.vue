<template>
    <div class="hourly-forecast">
      <div class="forecast-items">
        <div class="forecast-item" v-for="hour in hourlyData" :key="hour.time">
          <p>{{ formatHour(hour.time) }}</p>
          <img :src="hour.condition.icon" alt="Weather Icon">
          <p v-if="temperatureUnit === 'C'">{{ formatTemperature(hour.temp_c) }}°</p>
            <p v-else>{{ formatTemperature(hour.temp_f) }}°</p>
        </div>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, PropType } from 'vue';
  
  export default defineComponent({
    name: 'HourlyForecast',
    props: {
      hourlyData: {
        type: Array as PropType<any[]>,
        required: true
      },
      temperatureUnit: {
        type: String as PropType<'C' | 'F'>,
        required: true
      }
    },
    methods: {
      formatTemperature(temp: number) {
        return temp % 1 >= 0.5 ? Math.ceil(temp) : Math.floor(temp);     
     },
      formatHour(time: string) {
        const date = new Date(time);
        return date.getHours() + ':' + (date.getMinutes() < 10 ? '0' : '') + date.getMinutes();
      }
    }
  });
  </script>
  
  <style scoped>
  .hourly-forecast {
    padding: 10px;
  }
  
  .forecast-items {
    display: flex;
    overflow-x: scroll;
    gap: 15px;
    padding: 10px 0;
    scroll-snap-type: x mandatory;
  }
  
  .forecast-item {
    flex-shrink: 0;
    text-align: center;
    scroll-snap-align: center;

    p:last-child {
      font-weight: 600;
    }
  }
  
  .forecast-item img {
    width: 45px;
    height: 45px;
  }
  
  .forecast-items::-webkit-scrollbar {
    display: none;  
  }
  
  .forecast-items {
    -ms-overflow-style: none;  
    scrollbar-width: none;  
  }
  </style>
  