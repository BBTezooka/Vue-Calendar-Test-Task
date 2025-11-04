<template>
  <div class="wrapper">
    <header>
      <p class="current-date">
        {{ monthNames[currentLang][currentMonth] }} {{ currentYear }}
      </p>
      <div class="icons">
        <span @click="prevMonth" class="material-symbols-rounded">chevron_left</span>
        <span @click="nextMonth" class="material-symbols-rounded">chevron_right</span>
      </div>
    </header>

    <div class="calendar">
      <ul class="weeks">
        <li v-for="(day, i) in weekDays[currentLang]" :key="i">{{ day }}</li>
      </ul>

      <ul class="days">
        <li
          v-for="(day, index) in days"
          :key="index"
          :class="{
            inactive: day.inactive,
            active: isSelected(day)
          }"
          @click="selectDate(day)"
        >
          {{ day.date }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from "vue";

const props = defineProps({
  initialDate: { type: String, default: "" },
  lang: { type: String, default: "ru" }
});

const emit = defineEmits(["dateSelected"]);

const today = new Date();
const selectedDate = ref(null);
const currentLang = ref(props.lang);

const currentYear = ref(today.getFullYear());
const currentMonth = ref(today.getMonth());

const monthNames = {
  ru: ["Январь","Февраль","Март","Апрель","Май","Июнь","Июль","Август","Сентябрь","Октябрь","Ноябрь","Декабрь"],
  en: ["January","February","March","April","May","June","July","August","September","October","November","December"]
};

const weekDays = {
  ru: ["Вс","Пн","Вт","Ср","Чт","Пт","Сб"],
  en: ["Su","Mo","Tu","We","Th","Fr","Sa"]
};

onMounted(() => {
  if (props.initialDate) {
    const [y, m, d] = props.initialDate.split("-").map(Number);
    const init = new Date(y, m - 1, d);
    selectedDate.value = init;
    currentYear.value = y;
    currentMonth.value = m - 1;
  } else {
    selectedDate.value = today;
  }
});

const days = computed(() => {
  const firstDayOfMonth = new Date(currentYear.value, currentMonth.value, 1).getDay();
  const lastDateOfMonth = new Date(currentYear.value, currentMonth.value + 1, 0).getDate();
  const lastDayOfPrevMonth = new Date(currentYear.value, currentMonth.value, 0).getDate();

  const arr = [];

  for (let i = firstDayOfMonth === 0 ? 6 : firstDayOfMonth - 1; i > 0; i--) {
    arr.push({ date: lastDayOfPrevMonth - i + 1, inactive: true, monthOffset: -1 });
  }

 
  for (let i = 1; i <= lastDateOfMonth; i++) {
    arr.push({ date: i, inactive: false, monthOffset: 0 });
  }


  const totalCells = 42;
  while (arr.length < totalCells) {
    arr.push({ date: arr.length - (firstDayOfMonth + lastDateOfMonth) + 1, inactive: true, monthOffset: 1 });
  }

  return arr;
});

const prevMonth = () => {
  currentMonth.value--;
  if (currentMonth.value < 0) {
    currentMonth.value = 11;
    currentYear.value--;
  }
};

const nextMonth = () => {
  currentMonth.value++;
  if (currentMonth.value > 11) {
    currentMonth.value = 0;
    currentYear.value++;
  }
};

const isSelected = (day) => {
  if (!selectedDate.value) return false;
  const d = new Date(currentYear.value, currentMonth.value + day.monthOffset, day.date);
  return (
    d.getDate() === selectedDate.value.getDate() &&
    d.getMonth() === selectedDate.value.getMonth() &&
    d.getFullYear() === selectedDate.value.getFullYear()
  );
};

const selectDate = (day) => {
  const newDate = new Date(
    currentYear.value,
    currentMonth.value + day.monthOffset,
    day.date
  );

  selectedDate.value = newDate;
  currentYear.value = newDate.getFullYear();
  currentMonth.value = newDate.getMonth();
  
  const year = newDate.getFullYear();
  const month = String(newDate.getMonth() + 1).padStart(2, "0");
  const dayNum = String(newDate.getDate()).padStart(2, "0");
  emit("dateSelected", `${year}-${month}-${dayNum}`);
};


watch(() => props.lang, (newLang) => {
  currentLang.value = newLang;
});
</script>

<style scoped>
.wrapper {
  width: 320px;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.08);
  padding: 14px;
  font-family: Arial, sans-serif;
}

header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
}

.current-date {
  font-weight: 600;
}

.icons span {
  cursor: pointer;
  user-select: none;
  padding: 6px;
  border-radius: 6px;
}

.calendar ul {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  list-style: none;
  padding: 0;
  margin: 0;
  text-align: center;
}

.weeks li { font-weight: 700; margin-bottom: 8px; }

.days li {
  padding: 8px 6px;
  margin: 2px;
  border-radius: 6px;
  cursor: pointer;
  transition: background 0.15s;
}

.days li.inactive { color: #bbb; }

.days li.active { background: #2563eb; color: #fff; }

.days li:hover:not(.active) { background: #f0f0f0; }
</style>
