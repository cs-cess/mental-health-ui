<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const selectedMood = ref('');
const message = ref('');
const moodHistory = ref([]); 

// 1. PULL: Gets the data from Railway to show on your screen
const fetchMoods = async () => {
  try {
    const response = await axios.get('https://mood-tracker-api-ky3f.onrender.com/api/moods');
    moodHistory.value = response.data;
  } catch (error) {
    console.error("Error fetching history:", error);
  }
};

// 2. PUSH: Sends your click to the Railway database
const saveMood = async () => {
  if (!selectedMood.value) return;
  message.value = "Saving..."; 
  try {
    await axios.post('https://mood-tracker-api-ky3f.onrender.com/api/moods', {
      mood_text: selectedMood.value 
    });
    message.value = `Successfully saved: ${selectedMood.value}!`;
    fetchMoods(); // Refresh the list automatically so you see the change
  } catch (error) {
    message.value = "Error: Could not connect to the server.";
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

    <div v-if="selectedMood">
      <p>Selected: <strong>{{ selectedMood }}</strong></p>
      <button @click="saveMood" class="save-btn">Save to Database</button>
    </div>

    <h2 v-if="message" class="status-msg">{{ message }}</h2>

    <hr />

    <div class="history-card">
      <h3>Your Saved History</h3>
      <ul class="history-list">
        <li v-for="item in moodHistory" :key="item.id">
          <span v-if="item.mood_text" class="mood-entry">
             {{ item.mood_text }} <small>(ID: {{ item.id }})</small>
          </span>
          <span v-else class="old-entry">
            Old Test Entry <small>(ID: {{ item.id }})</small>
          </span>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.container { text-align: center; max-width: 500px; margin: 50px auto; font-family: 'Segoe UI', sans-serif; }
.buttons button { font-size: 1.2rem; margin: 8px; cursor: pointer; padding: 12px; border-radius: 10px; border: 1px solid #ddd; background: white; }
.save-btn { padding: 12px 30px; background: #28a745; color: white; border: none; border-radius: 5px; cursor: pointer; font-weight: bold; }
.status-msg { color: #666; font-size: 1rem; margin-top: 15px; }
hr { margin: 40px 0; border: 0; border-top: 1px solid #eee; }
.history-card { background: #f8f9fa; padding: 20px; border-radius: 15px; text-align: left; }
.history-list { list-style: none; padding: 0; }
.history-list li { padding: 10px 0; border-bottom: 1px solid #e9ecef; display: flex; justify-content: space-between; }
.mood-entry { font-weight: 600; color: #333; }
.old-entry { color: #bbb; font-style: italic; }
</style>