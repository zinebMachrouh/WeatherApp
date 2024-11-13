<template>
    <div class="weekly-forecast">
      <div v-for="day in dailyData" :key="day.date" class="day">
        <p class="day-name">{{ formatDate(day.date) }}</p>
        <img :src="day.day.condition.icon" alt="Weather Icon" />
        <!-- Display active and inactive temperatures based on computed properties -->
        <p class="temperature_active">{{ activeTemperature(day) }}°</p>
        <p class="temperature">{{ inactiveTemperature(day) }}°</p>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, PropType } from 'vue';
  
  export default defineComponent({
    name: 'WeeklyForecast',
    props: {
      dailyData: {
        type: Array as PropType<Array<any>>,
        required: true
      },
      temperatureUnit: {
        type: String as PropType<'C' | 'F'>,
        required: true
      }
    },
    computed: {
      inactiveUnit() {
        return this.temperatureUnit === 'C' ? 'F' : 'C';
      }
    },
    methods: {
      formatDate(date: string) {
        const options: Intl.DateTimeFormatOptions = { weekday: 'short' };
        return new Date(date).toLocaleDateString(undefined, options);
      },
      activeTemperature(day: any) {
        return this.temperatureUnit === 'C' ? Math.round(day.day.avgtemp_c) : Math.round(day.day.avgtemp_f);
      },
      inactiveTemperature(day: any) {
        return this.temperatureUnit === 'C' ? Math.round(day.day.avgtemp_f) : Math.round(day.day.avgtemp_c);
      }
    }
  });
  </script>
  
  <style scoped>
  .weekly-forecast {
    display: flex;
    overflow-x: auto;
    align-items: center;
    justify-content: space-between;
    padding: 10px 0;
    scrollbar-width: none;
  }
  .weekly-forecast::-webkit-scrollbar {
    display: none;
  }
  
  .day {
    display: flex;
    flex-direction: column;
    align-items: center;
    min-width: 30%;
    text-align: center;
  }
  
  .day img {
    width: 50px;
    height: 50px;
    margin-bottom: 5px;
  }
  
  .temperature_active {
    font-weight: 600;
    margin-top: 5px;
  }
  
  .temperature {
    font-weight: 600;
    color: #aaacb2;
  }
  </style>
  