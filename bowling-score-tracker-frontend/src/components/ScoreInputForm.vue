<template>
  <form @submit.prevent="handleSubmit">
    <div>
      <label for="date">날짜:</label>
      <input type="date" v-model="playedAt" id="date" required />
    </div>
    <div>
      <label for="game1">1게임 점수:</label>
      <input type="number" v-model.number="game1" id="game1" min="0" required />
    </div>
    <div>
      <label for="game2">2게임 점수:</label>
      <input type="number" v-model.number="game2" id="game2" min="0" required />
    </div>
    <div>
      <label for="game3">3게임 점수:</label>
      <input type="number" v-model.number="game3" id="game3" min="0" required />
    </div>
    <button type="submit">저장</button>
    <div v-if="message" :class="{ error: isError, success: !isError }">{{ message }}</div>
  </form>
</template>

<script setup>
import { ref, defineEmits } from 'vue';
import { supabase } from '../lib/supabase.js';

const playedAt = ref('');
const game1 = ref(0);
const game2 = ref(0);
const game3 = ref(0);
const message = ref('');
const isError = ref(false);
const emit = defineEmits(['saved']);

const handleSubmit = async () => {
  message.value = '';
  isError.value = false;
  const { error } = await supabase.from('games').insert([
    {
      played_at: playedAt.value,
      game1_score: game1.value,
      game2_score: game2.value,
      game3_score: game3.value,
    },
  ]);
  if (error) {
    message.value = '저장 실패: ' + error.message;
    isError.value = true;
  } else {
    message.value = '저장 성공!';
    playedAt.value = '';
    game1.value = 0;
    game2.value = 0;
    game3.value = 0;
    emit('saved');
  }
};
</script>

<style scoped>
form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  max-width: 300px;
  width: 100%;
  padding: 1rem;
}
label {
  font-weight: bold;
}
input {
  width: 100%;
  padding: 0.5rem;
  box-sizing: border-box;
}
button {
  padding: 0.5rem;
  font-size: 1rem;
  width: 100%;
}
.success {
  color: green;
}
.error {
  color: red;
}
@media (max-width: 600px) {
  form {
    max-width: 100vw;
    padding: 0.5rem;
  }
}
</style> 