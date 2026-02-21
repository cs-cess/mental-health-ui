<script setup>
import { ref } from 'vue';
import axios from 'axios';

const selectedMood = ref('');
const message = ref('');

const saveMood = async () => {
  message.value = "Saving..."; 
  try {
    // We changed 'mood' to 'mood_text' to match your backend and database
    await axios.post('https://mood-tracker-api-ky3f.onrender.com/api/moods', {
      mood_text: selectedMood.value 
    });
    message.value = `Successfully saved: ${selectedMood.value}!`;
  } catch (error) {
    message.value = "Error: Could not connect to the server.";
    console.error(error);
  }
};
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
  </div>
</template>

<style scoped>
.container { text-align: center; margin-top: 50px; font-family: sans-serif; }
.buttons button { font-size: 1.5rem; margin: 10px; cursor: pointer; }
</style>