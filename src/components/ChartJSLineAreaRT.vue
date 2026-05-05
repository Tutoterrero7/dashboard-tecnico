<template>
  <div class="streaming-chart">
    <canvas ref="canvasRef"></canvas>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { Chart, LineController, LineElement, PointElement, LinearScale, TimeScale, Title, Tooltip, Filler } from 'chart.js';
import streamingPlugin from 'chartjs-plugin-streaming';
import 'chartjs-adapter-date-fns';

const props = withDefaults(defineProps<{
  chartType?: 'line' | 'area';
  title?: string;
  color?: string;
  min?: number;
  max?: number;
}>(), {
  chartType: 'line',
  title: 'Carga del servidor',
  color: '#10b981',
  min: 0,
  max: 100,
});

const canvasRef = ref<HTMLCanvasElement | null>(null);
let chartInstance: Chart | null = null;

onMounted(() => {
  if (!canvasRef.value) return;
  const isArea = props.chartType === 'area';

  Chart.register(LineController, LineElement, PointElement, LinearScale, TimeScale, Title, Tooltip, streamingPlugin);
  if (isArea) Chart.register(Filler);

  chartInstance = new Chart(canvasRef.value, {
    type: 'line',
    data: {
      datasets: [{
        label: props.title,
        borderColor: props.color,
        backgroundColor: isArea ? `${props.color}40` : 'transparent',
        borderWidth: 2,
        fill: isArea,
        tension: 0.3,
        data: []
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      animation: false,
      scales: {
        x: {
          type: 'realtime',
          realtime: {
            duration: 20000,
            refresh: 1000,
            delay: 1000,
            onRefresh(chart) {
              chart.data.datasets[0].data.push({
                x: Date.now(),
                y: Math.floor(Math.random() * (props.max - props.min + 1) + props.min),
              });
            }
          },
          grid: { color: 'rgba(203, 213, 225, 0.1)' },
          ticks: { color: '#8C8C8C', maxRotation: 0 }
        },
        y: {
          min: props.min,
          max: props.max,
          grid: { color: 'rgba(203, 213, 225, 0.1)' },
          ticks: { color: '#8C8C8C', stepSize: 20 }
        }
      },
      plugins: {
        legend: { labels: { color: '#e2e8f0', usePointStyle: true } },
        title: { display: true, text: props.title, color: '#8C8C8C', align: 'start', font: { size: 16, weight: 'bold' }, padding: { top: 10, bottom: 20 } },
        tooltip: {
          enabled: true,
          mode: 'nearest',
          intersect: false,
          backgroundColor: 'rgba(15, 23, 42, 0.8)',
          titleColor: '#8C8C8C',
          bodyColor: '#8C8C8C',
          callbacks: { label: (ctx) => `Carga: ${ctx.parsed.y}%` }
        }
      },
      elements: { point: { radius: 0, hoverRadius: 6, hitRadius: 10 }, line: { borderWidth: 2, borderJoinStyle: 'round' } }
    }
  });
});

onBeforeUnmount(() => {
  if (chartInstance) {
    chartInstance.destroy();
    chartInstance = null;
  }
});
</script>

<style scoped>
.streaming-chart {
  width: 100%;
  height: 100%;
  min-height: 180px;
  padding: 8px;
}
.streaming-chart canvas {
  width: 100% !important;
  height: 100% !important;
}
</style>