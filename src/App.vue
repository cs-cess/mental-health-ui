<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const selectedMood = ref('');
const message = ref('');
const moodHistory = ref([]); // To store the history from Railway

// Function to fetch all moods from your database
const fetchMoods = async () => {
  try {
    const response = await axios.get('https://mood-tracker-api-ky3f.onrender.com/api/moods');
    moodHistory.value = response.data;
  } catch (error) {
    console.error("Error fetching history:", error);
  }
};

const saveMood = async () => {
  if (!selectedMood.value) return;
  message.value = "Saving..."; 
  try {
    // Sends data to the mood_text column
    await axios.post('https://mood-tracker-api-ky3f.onrender.com/api/moods', {
      mood_text: selectedMood.value 
    });
    message.value = `Successfully saved: ${selectedMood.value}!`;
    fetchMoods(); // Refresh the list automatically
  } catch (error) {
    message.value = "Error: Could not connect to the server.";
  }
};

// Fetch moods when the page first loads
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

    <h2 v-if="message">{{ message }}</h2>

    <hr />
    <h3>Your History from Railway</h3>
    <ul class="history-list">
      <li v-for="item in moodHistory" :key="item.id">
        {{ item.mood_text }} <small>(ID: {{ item.id }})</small>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.container { text-align: center; margin-top: 50px; font-family: sans-serif; }
.buttons button { font-size: 1.5rem; margin: 10px; cursor: pointer; padding: 10px; border-radius: 8px; }
.save-btn { padding: 12px 24px; background: #28a745; color: white; cursor: pointer; border: none; border-radius: 5px; font-weight: bold; }
.history-list { list-style: none; padding: 0; margin-top: 20px; }
.history-list li { padding: 10px; border-bottom: 1px solid #ddd; width: 60%; margin: 0 auto; color: #555; }
hr { margin: 40px 0; border: 0; border-top: 1px solid #eee; }
</style>