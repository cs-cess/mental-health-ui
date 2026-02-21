<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const selectedMood = ref('');
const message = ref('');
const moodHistory = ref([]); 

// 1. FETCH: Pulls the clean data (IDs 6-9) from Railway
const fetchMoods = async () => {
  try {
    const response = await axios.get('https://mood-tracker-api-ky3f.onrender.com/api/moods');
    moodHistory.value = response.data;
  } catch (error) {
    console.error("Error fetching history:", error);
  }
};

// 2. SAVE: Sends "Happy", "Sad", or "Neutral" to the mood_text column
const saveMood = async () => {
  if (!selectedMood.value) return;
  message.value = "Saving to database..."; 
  try {
    await axios.post('https://mood-tracker-api-ky3f.onrender.com/api/moods', {
      mood_text: selectedMood.value 
    });
    message.value = `Successfully saved: ${selectedMood.value}!`;
    selectedMood.value = ''; // Clear the selection after saving
    fetchMoods(); // Refresh the list so the new mood appears instantly
  } catch (error) {
    message.value = "Error: Could not save to database.";
  }
};

// Load history immediately when the page opens
onMounted(fetchMoods);
</script>

<template>
  <div class="container">
    <header>
      <h1>Mental Health Tracker</h1>
      <p>How are you feeling today?</p>
    </header>

    <div class="mood-selector">
      <button @click="selectedMood = 'Happy'" :class="{ active: selectedMood === 'Happy' }">😊 Happy</button>
      <button @click="selectedMood = 'Sad'" :class="{ active: selectedMood === 'Sad' }">😢 Sad</button>
      <button @click="selectedMood = 'Neutral'" :class="{ active: selectedMood === 'Neutral' }">😐 Neutral</button>
    </div>

    <div v-if="selectedMood" class="confirm-box">
      <p>You selected: <strong>{{ selectedMood }}</strong></p>
      <button @click="saveMood" class="save-btn">Save Mood</button>
    </div>

    <p v-if="message" class="status-message">{{ message }}</p>

    <hr />

    <section class="history-section">
      <h2>History Log (from Railway)</h2>
      <ul class="history-list">
        <li v-for="item in moodHistory" :key="item.id">
          <div class="history-item" v-if="item.mood_text">
            <span class="mood-label">{{ item.mood_text }}</span>
            <span class="id-badge">ID: {{ item.id }}</span>
          </div>
        </li>
      </ul>
      <p v-if="moodHistory.length === 0" class="empty-msg">No history found yet.</p>
    </section>
  </div>
</template>

<style scoped>
.container { text-align: center; max-width: 500px; margin: 50px auto; font-family: 'Inter', sans-serif; color: #333; }
h1 { color: #2c3e50; margin-bottom: 5px; }
.mood-selector { display: flex; justify-content: center; gap: 10px; margin: 20px 0; }
.mood-selector button { font-size: 1.1rem; padding: 15px; cursor: pointer; border-radius: 12px; border: 1px solid #ddd; background: white; transition: 0.2s; }
.mood-selector button.active { border-color: #22c55e; background: #f0fdf4; transform: scale(1.05); }
.confirm-box { background: #f8fafc; padding: 20px; border-radius: 15px; margin-bottom: 20px; }
.save-btn { padding: 12px 25px; background: #22c55e; color: white; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; }
.status-message { font-style: italic; color: #64748b; }
hr { margin: 40px 0; border: 0; border-top: 2px solid #f1f5f9; }
.history-section { text-align: left; }
.history-list { list-style: none; padding: 0; }
.history-item { display: flex; justify-content: space-between; align-items: center; padding: 12px 15px; background: #fff; border-bottom: 1px solid #f1f5f9; }
.mood-label { font-weight: 600; font-size: 1.1rem; }
.id-badge { background: #e2e8f0; padding: 2px 8px; border-radius: 4px; font-size: 0.75rem; color: #475569; }
.empty-msg { text-align: center; color: #94a3b8; margin-top: 20px; }
</style>