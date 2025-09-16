<template>
  <div class="calendar">
    <h1 class="title">🌸 Pastel Mood Calendar</h1>
    <div class="grid">
      <div
        v-for="day in daysInMonth"
        :key="day"
        class="day"
        @click="selectDay(day)"
        :style="{ backgroundColor: moodColors[moods[day]] || '#fff' }"
      >
        {{ day }}
      </div>
    </div>

    <div v-if="selectedDay" class="mood-picker">
      <h2>Select Mood for Day {{ selectedDay }}</h2>
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
import { ref, onMounted } from "vue";

const daysInMonth = 30; // simple month (can later auto calc)
const selectedDay = ref(null);
const moods = ref({});
const moodColors = {
  Happy: "#FFD6E8", // pastel pink
  Calm: "#D6F5E3",  // pastel mint
  Sad: "#D6E0FF",   // pastel blue
  Tired: "#FFF5CC"  // pastel yellow
};

// Load from localStorage
onMounted(() => {
  const saved = localStorage.getItem("moods");
  if (saved) moods.value = JSON.parse(saved);
});

function selectDay(day) {
  selectedDay.value = day;
}

function setMood(day, mood) {
  moods.value[day] = mood;
  localStorage.setItem("moods", JSON.stringify(moods.value));
  selectedDay.value = null;
}
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

.grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 10px;
  margin-top: 20px;
}

.day {
  border: 1px solid #eee;
  border-radius: 10px;
  padding: 15px;
  cursor: pointer;
  font-weight: bold;
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
}
</style>
