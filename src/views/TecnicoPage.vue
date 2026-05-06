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
        <!-- Fila 1: Sparklines (3 KPIs rápidos) -->
        <ion-row class="ion-row-1">
          <ion-col size="12" size-lg="4">
            <div class="box">
              <!-- Tiempo de Carga de Pantallas -->
              <spark-line v-bind="sparkData1" />
            </div>
          </ion-col>
          <ion-col size="6" size-lg="4">
            <div class="box">
              <!-- Crash Rate -->
              <spark-line v-bind="sparkData2" />
            </div>
          </ion-col>
          <ion-col size="6" size-lg="4">
            <div class="box">
              <!-- Latencia de Resultados -->
              <spark-line v-bind="sparkData3" />
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 2: Uptime (Gauge + línea RT) -->
        <ion-row class="ion-row-2">
          <ion-col size="12" size-md="3" push-md="9">
            <div class="box">
              <!-- Uptime actual -->
              <EchartsGauge :value="currentValue" title="UPTIME" />
            </div>
          </ion-col>
          <ion-col size="12" size-md="9" pull-md="3">
            <div class="box">
              <!-- Uptime histórico en tiempo real -->
              <ApexLineRT
                :series="series"
                title="Uptime histórico"
                :kpi-target="99.9"
                color="#3b82f6"
              />
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 3: Rendimiento Sistema Fantasy -->
        <ion-row class="ion-row-3">
          <ion-col size="12" size-lg="4.5">
            <div class="box">
              <!-- Tiempo de cálculo de puntos tras cada carrera -->
              <ChartJSLineAreaRT
                chartType="line"
                title="Tiempo cálculo puntos Fantasy (s)"
                color="#10b981"
                :min="0"
                :max="10"
              />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4.5">
            <div class="box">
              <!-- Carga del sistema Fantasy -->
              <ChartJSLineAreaRT
                chartType="area"
                title="Carga sistema Fantasy (%)"
                color="#3b82f6"
                :min="0"
                :max="100"
              />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="3">
            <div class="box">
              <!-- Distribución de tiempos de cálculo -->
              <ApexDonut :segments="donutData" />
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
import ApexDonut from '@/components/ApexDonut.vue';
import ChartJSLineAreaRT from '@/components/ChartJSLineAreaRT.vue';

/********** CONSTANTES **********/
const UPDATE_INTERVAL = 1000;
const MAX_DATA_POINTS = 60;
let lastDate = Date.now();
let interval: ReturnType<typeof setInterval>;

/********** SPARKLINES **********/
/* 1) Tiempo de Carga de Pantallas */
const sparkData1 = ref({
  title: "Tiempo de carga de pantallas",
  value: "1.8s",
  bgColor: "gradient-blue",
  textColor: "white",
  iconName: "hourglass-outline",
  chartOptions: {
    chart: { type: 'area', sparkline: { enabled: true }, dropShadow: { enabled: true, top: 1, left: 1, blur: 2, opacity: 0.5 } },
    stroke: { curve: 'smooth', width: 3 },
    colors: ['#fff'],
    tooltip: {
      theme: 'dark',
      x: { show: false },
      y: { title: { formatter: () => 'Tiempo medio (s)' } }
    }
  },
  chartSeries: [{ data: [2.3, 2.1, 1.9, 1.7, 1.8, 1.9, 1.6, 1.7] }]
});

/* 2) Tasa de Errores (Crash Rate) */
const sparkData2 = ref({
  title: "Crash Rate",
  value: "0.7%",
  bgColor: "gradient-pink",
  textColor: "white",
  iconName: "alert-circle-outline",
  chartOptions: {
    chart: { type: 'bar', sparkline: { enabled: true }, dropShadow: { enabled: true, top: 1, left: 1, blur: 2, opacity: 0.5 } },
    stroke: { curve: 'smooth', width: 3 },
    colors: ['#fff'],
    tooltip: {
      theme: 'dark',
      x: { show: false },
      y: { title: { formatter: () => '% sesiones con error' } }
    }
  },
  chartSeries: [{ data: [1.3, 1.0, 0.9, 0.8, 0.7, 0.6, 0.7] }]
});

/* 3) Latencia de Actualización de Resultados */
const sparkData3 = ref({
  title: "Latencia resultados",
  value: "320ms",
  bgColor: "gradient-orange",
  textColor: "white",
  iconName: "timer-outline",
  chartOptions: {
    chart: { type: 'line', sparkline: { enabled: true }, dropShadow: { enabled: true, top: 1, left: 1, blur: 2, opacity: 0.5 } },
    stroke: { curve: 'straight', width: 3 },
    colors: ['#fff'],
    tooltip: {
      theme: 'dark',
      x: { show: false },
      y: { title: { formatter: () => 'Latencia (ms)' } }
    }
  },
  chartSeries: [{ data: [450, 410, 380, 360, 340, 320, 310, 330] }]
});

/********** APEX LINE RT -> UPTIME HISTÓRICO **********/
const data = ref<{ x: number; y: number }[]>([]);
const series = ref([{ name: 'Uptime (%)', data: data.value }]);

/********** GAUGE SIMPLE -> UPTIME ACTUAL **********/
const currentValue = ref(99.9);

/********** DONUT DATA **********/
const donutData = ref([
  { value: 85, name: 'España', color: '#f97316' },
  { value: 28, name: 'Mundo', color: '#10b981' }
]);

/********** REAL TIME LOGIC **********/
function addDataRealTime() {
  // Uptime entre 99.5% y 100%
  const newX = lastDate + UPDATE_INTERVAL;
  const newY = 99.5 + Math.random() * 0.5;
  const uptime = parseFloat(newY.toFixed(2));

  data.value.push({ x: newX, y: uptime });
  if (data.value.length > MAX_DATA_POINTS) {
    data.value = data.value.slice(-MAX_DATA_POINTS);
    series.value = [{ name: 'Uptime (%)', data: data.value }];
  }
  lastDate = newX;
  currentValue.value = uptime;



  donutData.value = [
  { value: 85, name: 'España', color: '#f97316' },
  { value: 15, name: 'Mundo', color: '#10b981' }
  ];
}

onMounted(() => {
  interval = setInterval(addDataRealTime, UPDATE_INTERVAL);
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
  height: auto;
}

ion-grid {
  height: auto;
  display: flex;
  flex-direction: column;
}

ion-row {
  margin-bottom: 10px;
}

.ion-row-1 {
  min-height: 100px;
  flex: 1;
}
.ion-row-2 {
  min-height: 140px;
  flex: 2;
}
.ion-row-3 {
  min-height: 130px;
  flex: 2;
  margin-top: 0px;
}

ion-col {
  --ion-grid-column-padding: 4px;
}

.box {
  background: #1e1e1e;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  transition: all 0.2s;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1px;
  width: 100%;
  height: 100%;
  min-height: 30px;
}

.box:hover {
  transform: translateY(-2px);
}

@media (max-width: 576px) {
  .ion-row-1 { min-height: 120px; }
  .ion-row-2 { min-height: 180px; }
  .ion-row-3 { min-height: 160px; }
  .box {
    min-height: 100px;
  }
}

@media (min-width: 768px) {
  .ion-row-1 { min-height: 110px; }
  .ion-row-2 { min-height: 170px; }
  .ion-row-3 { min-height: 150px; }
}
</style>