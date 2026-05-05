<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>📈 Técnico</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">🚀 Técnico</ion-title>
        </ion-toolbar>
      </ion-header>

      <ion-grid class="dashboard-grid">
        <!-- Fila 1: Sparklines -->
        <ion-row class="ion-row-1">
          <ion-col size="12" size-lg="4">
            <div class="box">
              <spark-line v-bind="sparkData1" />
            </div>
          </ion-col>
          <ion-col size="6" size-lg="4">
            <div class="box">
              <spark-line v-bind="sparkData2" />
            </div>
          </ion-col>
          <ion-col size="6" size-lg="4">
            <div class="box">
              <spark-line v-bind="sparkData3" />
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 2: ApexLineRT + EchartsGauge -->
        <ion-row class="ion-row-2">
          <ion-col size="12" size-md="3" push-md="9">
            <div class="box">
              <EchartsGauge :value="currentValue" title="USUARIOS ONLINE" />
            </div>
          </ion-col>
          <ion-col size="12" size-md="9" pull-md="3">
            <div class="box">
              <ApexLineRT :series="series" title="Usuarios online" :kpi-target="70" color="#3b82f6" />
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 3: Streaming + Gauge múltiple -->
        <ion-row class="ion-row-3">
          <ion-col size="12" size-lg="4.5">
            <div class="box">
              <ChartJSLineAreaRT chartType="line" title="Carga CPU" color="#10b981" :min="0" :max="100" />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4.5">
            <div class="box">
              <ChartJSLineAreaRT chartType="area" title="Memoria" color="#3b82f6" :min="50" :max="70" />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="3">
            <div class="box">
              <EchartsGaugeMultiple :segments="ringSegments" />
            </div>
          </ion-col>
        </ion-row>
      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonGrid, IonRow, IonCol } from '@ionic/vue';
import { ref, onMounted, onUnmounted } from 'vue';

import SparkLine from '@/components/SparkLine.vue';
import ApexLineRT from '@/components/ApexLineRT.vue';
import EchartsGauge from '@/components/EchartsGauge.vue';
import EchartsGaugeMultiple from '@/components/EchartsGaugeMultiple.vue';
import ChartJSLineAreaRT from '@/components/ChartJSLineAreaRT.vue';

/********** CONSTANTES **********/
const UPDATE_INTERVAL = 1000;
const MAX_DATA_POINTS = 60;
let lastDate = Date.now();
let interval: ReturnType<typeof setInterval>;

/********** SPARKLINES **********/
const sparkData1 = ref({
  title: "CLICKS",
  value: "1234",
  bgColor: "gradient-blue",
  textColor: "white",
  iconName: "navigate-outline",
  chartOptions: {
    chart: { type: 'area', sparkline: { enabled: true }, dropShadow: { enabled: true, top: 1, left: 1, blur: 2, opacity: 0.5 } },
    stroke: { curve: 'smooth', width: 3 },
    colors: ['#fff'],
    tooltip: { theme: 'dark', x: { show: false }, y: { title: { formatter: () => '' } } }
  },
  chartSeries: [{ data: [25, 66, 41, 59, 25, 44, 12, 36, 9, 21] }]
});

const sparkData2 = ref({
  title: "VIEWS",
  value: "1982",
  bgColor: "gradient-pink",
  textColor: "white",
  iconName: "eye-outline",
  chartOptions: {
    chart: { type: 'bar', sparkline: { enabled: true }, dropShadow: { enabled: true, top: 1, left: 1, blur: 2, opacity: 0.5 } },
    stroke: { curve: 'smooth', width: 3 },
    colors: ['#fff'],
    tooltip: { theme: 'dark', x: { show: false }, y: { title: { formatter: () => '' } } }
  },
  chartSeries: [{ data: [25, 66, 41, 59, 25, 44, 12, 36, 9, 21] }]
});

const sparkData3 = ref({
  title: "LEADS",
  value: "2011",
  bgColor: "gradient-orange",
  textColor: "white",
  iconName: "people-outline",
  chartOptions: {
    chart: { type: 'line', sparkline: { enabled: true }, dropShadow: { enabled: true, top: 1, left: 1, blur: 2, opacity: 0.5 } },
    stroke: { curve: 'straight', width: 3 },
    colors: ['#fff'],
    tooltip: { theme: 'dark', x: { show: false }, y: { title: { formatter: () => '' } } }
  },
  chartSeries: [{ data: [25, 66, 41, 59, 25, 44, 12, 36, 9, 21] }]
});

/********** APEX LINE RT **********/
const data = ref<{ x: number; y: number }[]>([]);
const series = ref([{ name: 'Usuarios', data: data.value }]);

/********** GAUGES **********/
const currentValue = ref(0);
const ringSegments = ref([
  { value: 0, name: '🥘 España', color: '#f97316', min: 80, max: 99 },
  { value: 0, name: '🌍 Mundo', color: '#10b981', min: 10, max: 30 }
]);

/********** REAL TIME LOGIC **********/
function addDataRealTime() {
  const newX = lastDate + UPDATE_INTERVAL;
  const newY = Math.floor(Math.random() * 90) + 10;
  data.value.push({ x: newX, y: newY });
  if (data.value.length > MAX_DATA_POINTS) {
    data.value = data.value.slice(-MAX_DATA_POINTS);
    series.value = [{ name: 'Usuarios', data: data.value }];
  }
  lastDate = newX;
  currentValue.value = newY;
  ringSegments.value.forEach((s) => {
    s.value = Math.floor(Math.random() * (s.max - s.min + 1)) + s.min;
  });
}

onMounted(() => {
  interval = setInterval(addDataRealTime, UPDATE_INTERVAL);
  // Forzar un resize después del primer render para que los gráficos calculen bien sus dimensiones
  setTimeout(() => {
    window.dispatchEvent(new Event('resize'));
  }, 100);
});

onUnmounted(() => {
  clearInterval(interval);
});
</script>

<style scoped>
ion-content {
  --background: #0a0a0a;
  height: 100%;
}

ion-grid {
  height: 100%;
  display: flex;
  flex-direction: column;
}

.ion-row-1 {
  min-height: 180px;
  flex: 2;
}
.ion-row-2 {
  min-height: 250px;
  flex: 5;
}
.ion-row-3 {
  min-height: 220px;
  flex: 3;
  margin-top: 10px;
}

ion-row {
  overflow: hidden;
  margin-bottom: 8px;
}

ion-col {
  max-height: 100%;
  --ion-grid-column-padding: 8px;
}

.box {
  background: #1e1e1e;
  border-radius: 16px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  transition: all 0.2s;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 8px;
  width: 100%;
  height: 100%;
  min-height: 150px;
}
.box:hover {
  transform: translateY(-2px);
}

@media (min-width: 768px) {
  .ion-row-1 { min-height: 200px; }
  .ion-row-2 { min-height: 300px; }
  .ion-row-3 { min-height: 250px; }
}
</style>