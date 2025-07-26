<template>
  <div>
    <h2>통계</h2>
    <div class="stats-box">
      <div>
        <strong>1게임 평균:</strong> <span class="avg-highlight">{{ avg.game1 }}</span><br />
        <strong>2게임 평균:</strong> <span class="avg-highlight">{{ avg.game2 }}</span><br />
        <strong>3게임 평균:</strong> <span class="avg-highlight">{{ avg.game3 }}</span><br />
        <strong>합산 평균:</strong> {{ avg.total }}
      </div>
      <div>
        <strong>1게임 최고점:</strong> {{ max.game1 }}<br />
        <strong>2게임 최고점:</strong> {{ max.game2 }}<br />
        <strong>3게임 최고점:</strong> {{ max.game3 }}<br />
        <strong>합산 최고점:</strong> {{ max.total }}
      </div>
    </div>
    <div class="chart-box">
      <Line v-if="chartData" :data="chartData" :options="chartOptions" :height="350" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, defineProps } from 'vue';
import { supabase } from '../lib/supabase.js';
import { Line } from 'vue-chartjs';
import {
  Chart,
  LineElement,
  PointElement,
  LinearScale,
  Title,
  CategoryScale,
  Legend,
  Tooltip
} from 'chart.js';

Chart.register(LineElement, PointElement, LinearScale, Title, CategoryScale, Legend, Tooltip);

const avg = ref({ game1: 0, game2: 0, game3: 0, total: 0 });
const max = ref({ game1: 0, game2: 0, game3: 0, total: 0 });
const chartData = ref(null);
const chartOptions = ref({
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { 
      position: 'top',
      labels: {
        color: '#333333',
        font: {
          size: 11
        },
        boxWidth: 15,
        padding: 10
      }
    },
    title: { 
      display: true, 
      text: '날짜별 점수 추이 및 3게임 평균',
      color: '#333333',
      font: {
        size: 13
      }
    }
  },
  scales: {
    x: {
      ticks: {
        color: '#666666',
        font: {
          size: 9
        },
        maxRotation: 45,
        minRotation: 45,
        maxTicksLimit: 8
      },
      grid: {
        color: '#e0e0e0'
      }
    },
    y: {
      ticks: {
        color: '#666666',
        font: {
          size: 9
        }
      },
      grid: {
        color: '#e0e0e0'
      }
    }
  },
  elements: {
    point: {
      radius: 3
    },
    line: {
      borderWidth: 2
    }
  },
  animation: {
    duration: 2000,
    easing: 'easeInOutQuart'
  },
  interaction: {
    intersect: false,
    mode: 'index'
  }
});

const props = defineProps({ refreshKey: Number });

const fetchStats = async () => {
  const { data, error } = await supabase
    .from('games')
    .select('*')
    .order('played_at', { ascending: true });
  if (!error && data && data.length) {
    // 평균/최고점 계산
    let sum1 = 0, sum2 = 0, sum3 = 0, sumTotal = 0;
    let max1 = 0, max2 = 0, max3 = 0, maxTotal = 0;
    const labels = [];
    const game1Arr = [];
    const game2Arr = [];
    const game3Arr = [];
    const dailyAvgArr = []; // 날짜별 3게임 평균
    
    data.forEach((row, idx) => {
      sum1 += row.game1_score;
      sum2 += row.game2_score;
      sum3 += row.game3_score;
      const total = row.game1_score + row.game2_score + row.game3_score;
      sumTotal += total;
      if (row.game1_score > max1) max1 = row.game1_score;
      if (row.game2_score > max2) max2 = row.game2_score;
      if (row.game3_score > max3) max3 = row.game3_score;
      if (total > maxTotal) maxTotal = total;
      
      labels.push(row.played_at);
      game1Arr.push(row.game1_score);
      game2Arr.push(row.game2_score);
      game3Arr.push(row.game3_score);
      
      // 해당 날짜의 3게임 평균
      const dailyAvg = ((row.game1_score + row.game2_score + row.game3_score) / 3).toFixed(1);
      dailyAvgArr.push(Number(dailyAvg));
    });
    
    const n = data.length;
    avg.value = {
      game1: (sum1 / n).toFixed(1),
      game2: (sum2 / n).toFixed(1),
      game3: (sum3 / n).toFixed(1),
      total: (sumTotal / n).toFixed(1)
    };
    max.value = {
      game1: max1,
      game2: max2,
      game3: max3,
      total: maxTotal
    };
    
    chartData.value = {
      labels,
      datasets: [
        {
          label: '1게임',
          data: game1Arr,
          borderColor: 'rgba(66, 185, 131, 0.7)',
          backgroundColor: 'rgba(66, 185, 131, 0.1)',
          tension: 0.2,
          borderWidth: 2,
          pointRadius: 3,
          order: 1
        },
        {
          label: '2게임',
          data: game2Arr,
          borderColor: 'rgba(255, 152, 0, 0.7)',
          backgroundColor: 'rgba(255, 152, 0, 0.1)',
          tension: 0.2,
          borderWidth: 2,
          pointRadius: 3,
          order: 2
        },
        {
          label: '3게임',
          data: game3Arr,
          borderColor: 'rgba(33, 150, 243, 0.7)',
          backgroundColor: 'rgba(33, 150, 243, 0.1)',
          tension: 0.2,
          borderWidth: 2,
          pointRadius: 3,
          order: 3
        },
        {
          label: '날짜별 3게임 평균',
          data: dailyAvgArr,
          borderColor: '#e91e63',
          backgroundColor: 'rgba(233, 30, 99, 0.1)',
          borderWidth: 4,
          tension: 0.3,
          pointRadius: 6,
          pointBackgroundColor: '#e91e63',
          pointBorderColor: '#ffffff',
          pointBorderWidth: 3,
          pointHoverRadius: 8,
          pointHoverBackgroundColor: '#c2185b',
          pointHoverBorderColor: '#ffffff',
          pointHoverBorderWidth: 3,
          borderDash: [5, 3],
          fill: false,
          order: 0,
          shadowOffsetX: 3,
          shadowOffsetY: 3,
          shadowBlur: 6,
          shadowColor: 'rgba(233, 30, 99, 0.3)'
        }
      ]
    };
  }
};

onMounted(fetchStats);
watch(() => props.refreshKey, fetchStats);
</script>

<style scoped>
.stats-box {
  display: flex;
  gap: 2rem;
  margin-bottom: 2rem;
}
.avg-highlight {
  color: #e91e63;
  font-size: 1.5rem;
  font-weight: bold;
  background: #fff3f8;
  border-radius: 0.3em;
  padding: 0.1em 0.5em;
  margin-left: 0.2em;
}
.chart-box {
  max-width: 700px;
  margin: 0 auto;
  height: 400px;
  width: 100%;
  background: #ffffff;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  border: 1px solid #e0e0e0;
  overflow: hidden;
  box-sizing: border-box;
}
@media (max-width: 600px) {
  .stats-box {
    flex-direction: column;
    gap: 1rem;
    font-size: 0.95rem;
  }
  .chart-box {
    max-width: calc(100vw - 3rem);
    width: calc(100vw - 3rem);
    height: 300px;
    min-width: 0;
    padding: 8px;
    margin: 0 0.5rem;
    overflow-x: hidden;
  }
  .avg-highlight {
    font-size: 1.1rem;
    padding: 0.1em 0.3em;
  }
}
</style> 