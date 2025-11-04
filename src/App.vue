<template>
  <div style="padding:20px; font-family: Arial, sans-serif;">
    <h2>Vue 3 Calendar Demo</h2>

    <Calendar
      :lang="lang"
      initialDate="2025-11-04"
      @dateSelected="onDateSelected"
    />

    <p style="margin-top:16px;">
      Выбранная дата: <strong>{{ picked }}</strong>
    </p>

    <div style="margin-top:12px;">
      <button @click="lang = 'ru'">Русский</button>
      <button @click="lang = 'en'" style="margin-left:8px">English</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Calendar from './components/Calendar.vue'

const picked = ref('')
const lang = ref('ru') 

function onDateSelected(date) {
  picked.value = date
}
</script>
