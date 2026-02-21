<script setup>
import { ref } from 'vue';
import axios from 'axios';

const selectedMood = ref('');
const message = ref('');

const saveMood = async () => {
  message.value = "Saving..."; // This lets you know the button was clicked!
  try {
    await axios.post('https://mood-tracker-api-ky3f.onrender.com/api/moods', {
      mood: selectedMood.value
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
      <button @click="saveMood" style="padding: 10px; background: green; color: white;">
        Save to Database
      </button>
    </div>

    <h2 v-if="message">{{ message }}</h2>
  </div>
</template>