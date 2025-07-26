<template>
  <div>
    <h2>점수 기록</h2>
    <table v-if="scores.length">
      <thead>
        <tr>
          <th>날짜</th>
          <th>1게임</th>
          <th>2게임</th>
          <th>3게임</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="score in scores" :key="score.id">
          <td>{{ score.played_at }}</td>
          <td>{{ score.game1_score }}</td>
          <td>{{ score.game2_score }}</td>
          <td>{{ score.game3_score }}</td>
        </tr>
      </tbody>
    </table>
    <div v-else>기록이 없습니다.</div>
  </div>
</template>

<script setup>
import { ref, onMounted, defineProps, watch } from 'vue';
import { supabase } from '../lib/supabase.js';

const props = defineProps({ refreshKey: Number });

const scores = ref([]);

const fetchScores = async () => {
  const { data, error } = await supabase
    .from('games')
    .select('*')
    .order('played_at', { ascending: false });
  if (!error && data) {
    scores.value = data;
  }
};

onMounted(fetchScores);
watch(() => props.refreshKey, fetchScores);
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 2rem;
}
th, td {
  border: 1px solid #ccc;
  padding: 0.5rem;
  text-align: center;
}
th {
  background: #f5f5f5;
}
@media (max-width: 600px) {
  table {
    font-size: 0.95rem;
    display: block;
    overflow-x: auto;
    white-space: nowrap;
  }
}
</style> 