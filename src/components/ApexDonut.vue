<template>
  <div class="donut-container">
    <VueApexCharts
      type="donut"
      height="100%"
      width="100%"
      :options="chartOptions"
      :series="series"
    />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import VueApexCharts from 'vue3-apexcharts';
import type { ApexOptions } from 'apexcharts';

const props = defineProps<{
  segments: { value: number; name: string; color: string }[];
}>();

const series = computed(() => props.segments.map(s => s.value));

const chartOptions = computed<ApexOptions>(() => ({
  chart: {
    type: 'donut' as const,
    background: 'transparent',
    toolbar: { show: false }
  },
  labels: props.segments.map(s => s.name),
  colors: props.segments.map(s => s.color),
  legend: {
    position: 'bottom',
    labels: { colors: '#8C8C8C', useSeriesColors: false },
    fontSize: '12px'
  },
  dataLabels: {
    enabled: true,
    style: { colors: ['#fff'], fontSize: '11px' },
    dropShadow: { enabled: false }
  },
  tooltip: { theme: 'dark' },
  plotOptions: {
    pie: {
      donut: {
        size: '65%',
        labels: {
          show: true,
          total: {
            show: true,
            label: 'Total',
            color: '#ddd',
            fontSize: '14px'
          }
        }
      }
    }
  },
  title: {
    text: 'Procedencia',
    align: 'center',
    style: { color: '#8C8C8C', fontSize: '14px', fontWeight: 'bold' }
  }
}));
</script>

<style scoped>
.donut-container {
  width: 100%;
  height: 100%;
  min-height: 180px;   /* más pequeño para que encaje */
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 4px;
}
</style>