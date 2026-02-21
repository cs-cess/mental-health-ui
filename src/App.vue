<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const selectedMood = ref('');
const message = ref('');
const moodHistory = ref([]); 

const fetchMoods = async () => {
  try {
    const response = await axios.get('https://mood-tracker-api-ky3f.onrender.com/api/moods');
    // Sort so newest moods appear at the top
    moodHistory.value = response.data;
  } catch (error) {
    console.error("Error fetching history:", error);
  }
};

const saveMood = async () => {
  if (!selectedMood.value) return;
  message.value = "Saving..."; 
  try {
    await axios.post('https://mood-tracker-api-ky3f.onrender.com/api/moods', {
      mood_text: selectedMood.value 
    });
    message.value = `Successfully saved: ${selectedMood.value}!`;
    selectedMood.value = ''; // Reset selection
    fetchMoods(); 
  } catch (error) {
    message.value = "Error saving mood.";
  }
};

onMounted(fetchMoods);
</script>

<template>
  <div class="container">
    <h1>Daily Mood Tracker</h1>
    
    <div class="buttons">
      <button @click="selectedMood = 'Happy'">😊 Happy</button>
      <button @click="selectedMood = 'Sad'">😢 Sad</button>
      <button @click="selectedMood = 'Neutral'">😐 Neutral</button>
    </div>

    <div v-if="selectedMood" class="save-area">
      <p>Current Selection: <strong>{{ selectedMood }}</strong></p>
      <button @click="saveMood" class="save-btn">Confirm & Save</button>
    </div>

    <p v-if="message" class="status">{{ message }}</p>

    <hr />

    <div class="history-container">
      <h3>History Log</h3>
      <ul class="history-list">
        <li v-for="item in moodHistory" :key="item.id">
          <span v-if="item.mood_text" class="entry-text">
            {{ item.mood_text }}
          </span>
          <span class="entry-id">ID: {{ item.id }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.container { text-align: center; max-width: 450px; margin: 50px auto; font-family: system-ui, sans-serif; }
.buttons button { font-size: 1.2rem; margin: 5px; cursor: pointer; padding: 15px; border-radius: 10px; border: 1px solid #ddd; background: #fff; }
.save-area { margin-top: 20px; padding: 15px; background: #f0fdf4; border-radius: 10px; }
.save-btn { padding: 10px 20px; background: #22c55e; color: white; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; }
.status { color: #666; font-style: italic; }
hr { margin: 30px 0; border: 0; border-top: 1px solid #eee; }
.history-container { text-align: left; }
.history-list { list-style: none; padding: 0; }
.history-list li { display: flex; justify-content: space-between; padding: 12px; border-bottom: 1px solid #f3f4f6; }
.entry-text { font-weight: 500; color: #1f2937; }
.entry-id { color: #9ca3af; font-size: 0.8rem; }
</style>