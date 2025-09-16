<template>
  <div class="calendar">
    <h1 class="title">🌸 Pastel Mood Calendar</h1>

    <!-- Month Navigation -->
    <div class="header">
      <button @click="prevMonth">←</button>
      <h2>{{ monthNames[currentMonth] }} {{ currentYear }}</h2>
      <button @click="nextMonth">→</button>
    </div>

    <!-- Weekday Labels -->
    <div class="weekdays">
      <div v-for="day in weekDays" :key="day" class="weekday">{{ day }}</div>
    </div>

    <!-- Calendar Grid -->
    <div class="grid">
      <div
        v-for="(day, index) in calendarDays"
        :key="index"
        class="day"
        v-if="day"
        @click="selectDay(day)"
        :style="{ backgroundColor: moods[getKey(day)] || '#fff' }"
      >
        {{ day.getDate() }}
      </div>
      <div v-else class="day empty"></div>
    </div>

    <!-- Mood Picker -->
    <div v-if="selectedDay" class="mood-picker">
      <h2>Select Mood for {{ selectedDay.toDateString() }}</h2>
      <button
        v-for="(color, mood) in moodColors"
        :key="mood"
        @click="setMood(selectedDay, color)"
        :style="{ backgroundColor: color }"
      >
        {{ mood }}
      </button>
    </div>

    <!-- Mood Legend -->
    <div class="legend">
      <h3>🎨 Mood Legend</h3>
      <div v-for="(color, mood) in moodColors" :key="mood" class="legend-item">
        <span class="color-box" :style="{ backgroundColor: color }"></span>
        <span>{{ mood }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

// Current month/year
const today = new Date();
const currentMonth = ref(today.getMonth());
const currentYear = ref(today.getFullYear());
const selectedDay = ref(null);

// Mood data
const moods = ref({});
const moodColors = {
  "Happy": "#FFD6E8",  // pastel pink
  "Calm": "#D6F5E3",   // pastel mint
  "Sad": "#D6E0FF",    // pastel blue
  "Tired": "#FFF5CC"   // pastel yellow
};

// Month & weekday names
const monthNames = [
  "January","February","March","April","May","June",
  "July","August","September","October","November","December"
];
const weekDays = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];

// Load saved moods
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

function setMood(day, color) {
  moods.value[getKey(day)] = color;
  localStorage.setItem("moods", JSON.stringify(moods.value));
  selectedDay.value = null;
}

// Month navigation
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

// Generate calendar days
const calendarDays = computed(() => {
  const firstDay = new Date(currentYear.value, currentMonth.value, 1);
  const lastDay = new Date(currentYear.value, currentMonth.value + 1, 0);

  const days = [];

  // Empty slots for weekdays before first day
  for (let i = 0; i < firstDay.getDay(); i++) {
    days.push(null);
  }

  // Fill month days
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

.header button {
  padding: 8px 15px;
  border-radius: 10px;
  border: none;
  background: #a5d8ff;
  cursor: pointer;
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
  display: flex;
  align-items: center;
  justify-content: center;
}

.day:hover {
  transform: scale(1.1);
}

.empty {
  background: transparent;
  border: none;
  cursor: default;
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
  font-size: 16px;
}

.legend {
  margin-top: 30px;
  text-align: left;
  display: inline-block;
}

.legend-item {
  display: flex;
  align-items: center;
  margin: 5px 0;
}

.color-box {
  width: 20px;
  height: 20px;
  border-radius: 5px;
  margin-right: 10px;
  border: 1px solid #ccc;
}
</style>
