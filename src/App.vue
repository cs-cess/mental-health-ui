<script setup>
import { ref } from 'vue';
import axios from 'axios';

const selectedMood = ref('');
const message = ref('');

const saveMood = async () => {
  if (!selectedMood.value) return;
  message.value = "Saving..."; 
  
  try {
    // This sends the data with the EXACT name the database wants
    await axios.post('https://mood-tracker-api-ky3f.onrender.com/api/moods', {
      mood_text: selectedMood.value 
    });
    message.value = `Success! Saved ${selectedMood.value} to mood_text column.`;
  } catch (error) {
    message.value = "Error connecting to server.";
    console.error(error);
  }
};
</script>

<template>
  <div style="text-align: center; margin-top: 50px;">
    <h1>V3 FIX: Mood Tracker</h1>
    <div style="margin-bottom: 20px;">
      <button @click="selectedMood = 'Happy'">😊 Happy</button>
      <button @click="selectedMood = 'Sad'">😢 Sad</button>
    </div>

    <div v-if="selectedMood">
      <p>Selected: {{ selectedMood }}</p>
      <button @click="saveMood" style="background: green; color: white; padding: 10px;">
        SAVE NOW
      </button>
    </div>
    <h2 v-if="message">{{ message }}</h2>
  </div>
</template>