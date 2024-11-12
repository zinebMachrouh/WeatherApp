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
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, PropType } from 'vue'
  
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
      }
    }
  })
  </script>
