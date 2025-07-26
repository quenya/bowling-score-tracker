<script setup lang="ts">
import { ref } from 'vue';
import ScoreInputForm from './components/ScoreInputForm.vue'
import ScoreList from './components/ScoreList.vue'
import ScoreStats from './components/ScoreStats.vue'

const refreshKey = ref(0);
const handleSaved = () => {
  refreshKey.value++;
};
</script>

<template>
  <div class="container">
    <h1>볼링 점수 트래커</h1>
    <ScoreStats :key="`stats-${refreshKey}`" />
    <ScoreInputForm @saved="handleSaved" />
    <ScoreList :key="`list-${refreshKey}`" />
  </div>
</template>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 1rem;
}
h1 {
  text-align: center;
  margin-bottom: 2rem;
  font-size: 2.2rem;
}
@media (max-width: 600px) {
  .container {
    max-width: 100%;
    padding: 0.5rem;
  }
  h1 {
    font-size: 1.3rem;
    margin-bottom: 1rem;
  }
}
</style>
