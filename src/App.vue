<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const selectedMood = ref('');
const message = ref('');
const moodHistory = ref([]); // Added this to store moods from the database

// Function to fetch all moods from your Render API
const fetchMoods = async () => {
  try {
    const response = await axios.get('https://mood-tracker-api-ky3f.onrender.com/api/moods');
    moodHistory.value = response.data;
  } catch (error) {
    console.error("Could not fetch history", error);
  }
};

const saveMood = async () => {
  message.value = "Saving..."; 
  try {
    await axios.post('https://mood-tracker-api-ky3f.onrender.com/api/moods', {
      mood_text: selectedMood.value // This matches your Railway column!
    });
    message.value = `Successfully saved: ${selectedMood.value}!`;
    fetchMoods(); // Refresh the list automatically after saving
  } catch (error) {
    message.value = "Error: Could not connect to the server.";
    console.error(error);
  }
};

// This runs as soon as the page loads
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
      <button @click="saveMood" style="padding: 10px; background: green; color: white; cursor: pointer;">
        Save to Database
      </button>
    </div>

    <h2 v-if="message">{{ message }}</h2>

    <hr />

    <h3>History from Database</h3>
    <ul v-if="moodHistory.length">
      <li v-for="item in moodHistory" :key="item.id">
        {{ item.mood_text }} (ID: {{ item.id }})
      </li>
    </ul>
    <p v-else>No moods saved yet.</p>
  </div>
</template>

<style scoped>
.container { text-align: center; margin-top: 50px; font-family: sans-serif; }
.buttons button { font-size: 1.5rem; margin: 10px; cursor: pointer; }
ul { list-style: none; padding: 0; }
li { padding: 5px; border-bottom: 1px solid #ddd; width: 200px; margin: 0 auto; }
hr { margin: 30px 0; }
</style>