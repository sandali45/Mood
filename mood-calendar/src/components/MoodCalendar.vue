<template>
  <div class="calendar">
    <h1 class="title">🌸 Pastel Mood Calendar</h1>
    <div class="header">
      <button @click="prevMonth">←</button>
      <h2>{{ monthNames[currentMonth] }} {{ currentYear }}</h2>
      <button @click="nextMonth">→</button>
    </div>

    <div class="weekdays">
      <div v-for="day in weekDays" :key="day" class="weekday">{{ day }}</div>
    </div>

    <div class="grid">
      <div
        v-for="(day, index) in calendarDays"
        :key="index"
        class="day"
        @click="selectDay(day)"
        :style="{ backgroundColor: moodColors[moods[getKey(day)]] || '#fff' }"
      >
        {{ day.getDate() }}
      </div>
    </div>

    <div v-if="selectedDay" class="mood-picker">
      <h2>Select Mood for {{ selectedDay.toDateString() }}</h2>
      <button
        v-for="(color, mood) in moodColors"
        :key="mood"
        @click="setMood(selectedDay, mood)"
        :style="{ backgroundColor: color }"
      >
        {{ mood }}
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

// Month/year state
const today = new Date();
const currentMonth = ref(today.getMonth());
const currentYear = ref(today.getFullYear());
const selectedDay = ref(null);

// Moods
const moods = ref({});
const moodColors = {
 "#FFD6E8", // happy
"#D6F5E3", // calm
"#D6E0FF", // sad
"#FFF5CC"  // tired
};

const monthNames = [
  "January","February","March","April","May","June",
  "July","August","September","October","November","December"
];
const weekDays = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];

// Load moods from localStorage
onMounted(() => {
  const saved = localStorage.getItem("moods");
  if (saved) moods.value = JSON.parse(saved);
});

// Helpers
function getKey(date) {
  return `${date.getFullYear()}-${date.getMonth()}-${date.getDate()}`;
}

function selectDay(day) {
  selectedDay.value = day;
}

function setMood(day, mood) {
  moods.value[getKey(day)] = mood;
  localStorage.setItem("moods", JSON.stringify(moods.value));
  selectedDay.value = null;
}

function prevMonth() {
  if (currentMonth.value === 0) {
    currentMonth.value = 11;
    currentYear.value -= 1;
  } else {
    currentMonth.value -= 1;
  }
}

function nextMonth() {
  if (currentMonth.value === 11) {
    currentMonth.value = 0;
    currentYear.value += 1;
  } else {
    currentMonth.value += 1;
  }
}

// Generate days for current month
const calendarDays = computed(() => {
  const firstDay = new Date(currentYear.value, currentMonth.value, 1);
  const lastDay = new Date(currentYear.value, currentMonth.value + 1, 0);

  const days = [];

  // Fill empty slots before first day
  for (let i = 0; i < firstDay.getDay(); i++) {
    days.push(new Date(NaN)); // empty
  }

  // Fill real days
  for (let d = 1; d <= lastDay.getDate(); d++) {
    days.push(new Date(currentYear.value, currentMonth.value, d));
  }

  return days;
});
</script>

<style>
.calendar {
  text-align: center;
  font-family: "Poppins", sans-serif;
  background: #fffafc;
  min-height: 100vh;
  padding: 20px;
}

.title {
  color: #ffb6c1;
  font-size: 2rem;
}

.header {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 15px;
  margin-bottom: 10px;
}

.weekdays {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  font-weight: bold;
  margin-bottom: 5px;
}

.grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 5px;
}

.day {
  border: 1px solid #eee;
  border-radius: 10px;
  padding: 15px;
  cursor: pointer;
  min-height: 50px;
  transition: 0.3s;
}

.day:hover {
  transform: scale(1.1);
}

.mood-picker {
  margin-top: 20px;
}

.mood-picker button {
  border: none;
  border-radius: 10px;
  padding: 10px 15px;
  margin: 5px;
  cursor: pointer;
  font-weight: bold;
  transition: 0.2s;
  font-size: 20px;
}
</style>
