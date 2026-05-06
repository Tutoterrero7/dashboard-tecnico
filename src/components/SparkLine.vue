<template>
  <div :class="['box-sparkline', bgColor, textColor]">
    <div class="details">
      <div>
        <ion-icon :name="iconName"></ion-icon>
        <span>{{ title }}</span>
      </div>
      <span>{{ value }}</span>
    </div>
    <VueApexCharts
      class="sparkline-chart"
      :height="chartHeight"
      :options="chartOptions"
      :series="chartSeries"
    />
  </div>
</template>

<script setup lang="ts">
import { IonIcon } from '@ionic/vue';
import { addIcons } from 'ionicons';
import { 
  navigateOutline, 
  eyeOutline, 
  peopleOutline, 
  cashOutline, 
  logoIonic,
  hourglassOutline,
  alertCircleOutline,
  timerOutline
} from 'ionicons/icons';
import VueApexCharts from 'vue3-apexcharts';
import { ref, watchEffect, onUnmounted } from 'vue';

addIcons({
  'logo-ionic': logoIonic,
  'navigate-outline': navigateOutline,
  'eye-outline': eyeOutline,
  'people-outline': peopleOutline,
  'cash-outline': cashOutline,
  'hourglass-outline': hourglassOutline,
  'alert-circle-outline': alertCircleOutline,
  'timer-outline': timerOutline,
});

const props = defineProps({
  title: { type: String, default: 'Métrica' },
  value: { type: String, default: '#Value' },
  chartOptions: { type: Object, required: true },
  chartSeries: { type: Array, required: true },
  bgColor: { type: String, default: '' },
  textColor: { type: String, default: '' },
  iconName: { type: String, default: 'logo-ionic' },
});

// Control de altura responsiva
const chartHeight = ref('50%');
const updateChartHeight = () => {
  const width = window.innerWidth;
  if (width < 576) chartHeight.value = '30%';
  else if (width < 768) chartHeight.value = '40%';
  else chartHeight.value = '50%';
};
watchEffect(() => {
  updateChartHeight();
  window.addEventListener('resize', updateChartHeight);
});
onUnmounted(() => window.removeEventListener('resize', updateChartHeight));
</script>

<style scoped>
/* (estilos sin cambios) */
.box-sparkline {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 100%;
  width: 100%;
  padding: 16px;
  border-radius: 5px;
  container: box / inline-size;
}
.details {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 5px;
}
.details > div {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
}
.details > div > ion-icon {
  font-size: 2rem;
  --ionicon-stroke-width: 24px;
}
.details > div > span { font-size: .8rem; }
.details > span { font-size: 2.9rem; }
.sparkline-chart { min-width: 50px; width: 100%; }
@container box (width >= 324px) {
  .details {
    flex-direction: row;
    justify-content: flex-start;
    align-items: start;
    gap: 16px;
  }
  .details > span { font-size: 6cqmax; }
  .details > div > ion-icon { font-size: 4cqmax; }
  .details > div > span { font-size: 2cqmax; }
}
</style>