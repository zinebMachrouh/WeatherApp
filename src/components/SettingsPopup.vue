<template>
    <div class="popup" v-if="isPopupVisible">
      <section>
        <h4>Temperature</h4>
        <div class="switch">
          <button
            :class="{ active: temperatureUnit === 'C' }"
            @click="$emit('update:temperatureUnit', 'C')"
          >
            °C
          </button>
          <button
            :class="{ active: temperatureUnit === 'F' }"
            @click="$emit('update:temperatureUnit', 'F')"
          >
            °F
          </button>
        </div>
      </section>
      <section>
        <h4>Measurements</h4>
        <div class="switch">
          <button
            :class="{ active: measurementUnit === 'metric' }"
            @click="$emit('update:measurementUnit', 'metric')"
          >
            Metric
          </button>
          <button
            :class="{ active: measurementUnit === 'imperial' }"
            @click="$emit('update:measurementUnit', 'imperial')"
          >
            Imperial
          </button>
        </div>
      </section>
    </div>
</template>
  
<script lang="ts">
  import { defineComponent, PropType } from 'vue'
  
  export default defineComponent({
    name: 'SettingsPopup',
    props: {
      isPopupVisible: {
        type: Boolean,
        required: true
      },
      temperatureUnit: {
        type: String as PropType<'C' | 'F'>,
        required: true
      },
      measurementUnit: {
        type: String as PropType<'metric' | 'imperial'>,
        required: true
      }
    }
  })
</script>
