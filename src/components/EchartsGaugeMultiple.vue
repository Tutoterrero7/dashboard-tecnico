<template>
  <VEChart class="gauge-ring-chart" :option="chartOptions" autoresize />
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { use } from 'echarts/core';
import { GaugeChart } from 'echarts/charts';
import { CanvasRenderer } from 'echarts/renderers';
import { TooltipComponent, TitleComponent } from 'echarts/components';
import VEChart from 'vue-echarts';

use([GaugeChart, CanvasRenderer, TooltipComponent, TitleComponent]);

const props = withDefaults(defineProps<{
  segments: { value: number; name?: string; color?: string; min?: number; max?: number }[];
}>(), {
  segments: () => [
    { value: 50, name: 'Segmento 1', color: '#3b82f6', min: 0, max: 100 },
    { value: 70, name: 'Segmento 2', color: '#10b981', min: 0, max: 100 }
  ]
});

const chartOptions = computed(() => ({
  title: {
    text: 'Procedencia',
    left: 5,
    textStyle: { color: '#8C8C8C', fontWeight: 'bolder', fontSize: 16 }
  },
  series: props.segments.map((seg, idx) => ({
    type: 'gauge',
    startAngle: 90,
    endAngle: -270,
    radius: `${90 - idx * 20}%`,
    center: ['50%', `${55 - idx * 15}%`],
    pointer: { show: false },
    progress: { show: true, overlap: false, roundCap: true, clip: false, itemStyle: { borderWidth: 1, borderColor: '#464646' } },
    axisLine: { lineStyle: { width: 15, color: [[1, '#2d3748']] } },
    axisTick: { show: false },
    splitLine: { show: false },
    axisLabel: { show: false },
    detail: { show: false },
    title: { show: false },
    data: [{ value: seg.value, name: seg.name }],
    itemStyle: { color: seg.color || '#3b82f6' }
  }))
}));
</script>

<style scoped>
.gauge-ring-chart {
  width: 100%;
  height: 100%;
  min-height: 220px;
}
</style>