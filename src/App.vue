<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const selectedMood = ref('');
const message = ref('');
const moodHistory = ref([]); 

// Fetches the history from your Railway database via Render API
const fetchMoods = async () => {
  try {
    const response = await axios.get('https://mood-tracker-api-ky3f.onrender.com/api/moods');
    moodHistory.value = response.data;
  } catch (error) {
    console.error("Error fetching history:", error);
  }
};

// Saves the selected mood to the database
const saveMood = async () => {
  if (!selectedMood.value) return;
  message.value = "Saving..."; 
  try {
    // We send 'mood_text' to match the column name in Railway
    await axios.post('https://mood-tracker-api-ky3f.onrender.com/api/moods', {
      mood_text: selectedMood.value 
    });
    message.value = `Successfully saved: ${selectedMood.value}!`;
    fetchMoods(); // Refresh the list immediately
  } catch (error) {
    message.value = "Error: Could not connect to the server.";
  }
};

// Load history when the page opens
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

    <div v-if="selectedMood">
      <p>Selected: <strong>{{ selectedMood }}</strong></p>
      <button @click="saveMood" class="save-btn">Save to Database</button>
    </div>

    <h2 v-if="message" class="status">{{ message }}</h2>

    <hr />

    <div class="history-section">
      <h3>Mood History (from Railway)</h3>
      <ul class="history-list">
        <li v-for="item in moodHistory" :key="item.id">
          <span class="mood-display">{{ item.mood_text || "Empty Entry" }}</span>
          <span class="id-tag">ID: {{ item.id }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.container { text-align: center; max-width: 600px; margin: 50px auto; font-family: sans-serif; }
.buttons button { font-size: 1.5rem; margin: 10px; cursor: pointer; padding: 15px; border-radius: 12px; border: 1px solid #ddd; background: #fff; transition: 0.3s; }
.buttons button:hover { background: #f0f0f0; }
.save-btn { padding: 12px 24px; background: #28a745; color: white; cursor: pointer; border: none; border-radius: 8px; font-weight: bold; margin-top: 10px; }
.status { color: #555; font-size: 1.1rem; margin-top: 20px; }
hr { margin: 40px 0; border: 0; border-top: 1px solid #eee; }
.history-section { text-align: left; background: #f9f9f9; padding: 20px; border-radius: 10px; }
.history-list { list-style: none; padding: 0; }
.history-list li { display: flex; justify-content: space-between; padding: 10px; border-bottom: 1px solid #eee; }
.mood-display { font-weight: bold; color: #333; }
.id-tag { color: #888; font-size: 0.8rem; }
</style>