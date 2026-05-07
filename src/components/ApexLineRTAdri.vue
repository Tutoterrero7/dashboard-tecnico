<template>
  <div class="box-realtime-chart">
    <VueApexCharts height="100%" :options="chartOptions" :series="series" />
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, nextTick, watch } from 'vue';
import VueApexCharts from 'vue3-apexcharts';
import type { ApexOptions } from 'apexcharts';

const props = defineProps<{
  series: { name: string; data: { x: number; y: number }[] }[];
  title?: string;
  kpiTarget?: number;
  color?: string;
}>();

const title = computed(() => props.title ?? 'Gráfico Tiempo Real');
const chartColor = computed(() => props.color ?? '#34A72A');
const kpiTarget = computed(() => props.kpiTarget ?? 75);

const forceResize = () => nextTick(() => setTimeout(() => window.dispatchEvent(new Event('resize')), 300));
onMounted(forceResize);
watch(() => props.series?.[0]?.data?.length, (len) => { if (len && len === 1) forceResize(); });

const chartOptions = computed<ApexOptions>(() => {
  const data = props.series?.[0]?.data ?? [];
  const lastX = data.length > 0 ? data[data.length - 1].x : Date.now();
  const windowMs = 10000;
  return {
    chart: {
      id: 'realtime',
      type: 'line',
      animations: { enabled: true, easing: 'linear', dynamicAnimation: { speed: 1000 } },
      toolbar: { show: false },
      zoom: { enabled: false }
    },
    dataLabels: { enabled: false },
    stroke: { curve: 'smooth', width: 3 },
    colors: [chartColor.value],
    fill: { type: 'gradient', gradient: { shade: 'light', type: 'vertical', opacityFrom: 0.85, opacityTo: 0.55, stops: [0, 100] } },
    title: { text: title.value, align: 'left', style: { fontSize: '16px', fontWeight: 'bold', color: '#8C8C8C' } },
    markers: { size: 0, hover: { size: 4, sizeOffset: 3 } },
    xaxis: {
      type: 'datetime',
      min: lastX - windowMs,
      max: lastX,
      labels: { datetimeUTC: false, style: { colors: '#8C8C8C' }, datetimeFormatter: { hour: 'HH:mm', minute: 'HH:mm:ss' } },
      axisBorder: { show: false },
      axisTicks: { show: false }
    },
    yaxis: { max: 100, tickAmount: 5, labels: { style: { colors: '#8C8C8C' } } },
    legend: { show: false },
    tooltip: { x: { format: 'HH:mm:ss' }, theme: 'dark' },
    grid: { borderColor: '#334155', strokeDashArray: 5, padding: { left: 10, right: 10 } },
    annotations: {
      yaxis: [{
        y: kpiTarget.value,
        borderColor: chartColor.value,
        label: { borderColor: chartColor.value, style: { color: '#fff', background: chartColor.value }, text: `KPI - ${kpiTarget.value}` }
      }]
    }
  };
});
</script>

<style scoped>
.box-realtime-chart {
  display: flex;
  flex-direction: column;
  height: 100%;
  min-height: 290px;
  width: 100%;
  padding: 16px;
}
</style>