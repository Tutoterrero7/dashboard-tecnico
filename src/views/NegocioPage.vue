<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>🏎️ Negocio</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="ion-padding">
      <div class="dashboard-grid">

        <div class="box area-col1">
          <SparkLine :title="sparkData1.title" 
            :value="sparkData1.value" :bgColor="sparkData1.bgColor" 
            :textColor="sparkData1.textColor" 
            :iconName="sparkData1.iconName" 
            :chartOptions="sparkData1.chartOptions" 
            :chartSeries="sparkData1.chartSeries" />
        </div>

        <div class="box area-gauge">
          <EchartsGauge :value="dataGauge" title="Participación Fantasy" />
        </div>

        <div class="box area-grande">
          <ApexMixedChart :series="dataMixedChartSeries" />
        </div>

        <div class="box area-mapa">
          <EchartsMap :data="dataDownloadsEU" 
            title="Usuarios a nivel Mundial" 
            subtitle="Datos de los últimos 30 días" />
        </div>

        <div class="box area-spark">
          <ConversionFunnel />
        </div>

      </div>
    </ion-content>
  </ion-page>
</template>


<script setup lang="ts">
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonGrid, IonRow, IonCol } from '@ionic/vue';
import { ref, onMounted, onUnmounted } from 'vue'; // ← añadidos onMounted/onUnmounted
import EchartsMap from '@/components/EchartsMapAdri.vue';
import SparkLine from '@/components/SparkLineAdri.vue';
import EchartsGauge from '@/components/EchartsGaugeAdri.vue';
import ApexMixedChart from '@/components/ApexMixedChartAdri.vue';
// En imports:
import ConversionFunnel from '@/components/ConversionFunnelAdri.vue';
/***** 🛠️ CONSTANTES y VARIABLES *****/
const UPDATE_INTERVAL = 2000; // Cada 2s (negocio no necesita tanta frecuencia)
const MAX_ARPU_POINTS = 14;

let interval: ReturnType<typeof setInterval>;

/***** 🗃️ DATA *****/

// SparkLine ARPU - datos base
const arpuData = ref([7.40, 7.80, 8.10, 7.90, 8.30, 8.60, 8.20, 8.75, 9.10, 9.40, 9.65, 9.80, 9.90, 10.00]);

const sparkData1 = ref({
  title: "ARPU",
  value: "€10.00",
  bgColor: "gradient-blue",
  textColor: "white",
  iconName: "cash-outline",
  chartOptions: {
    chart: {
      id: 'arpu',
      type: 'area',
      sparkline: { enabled: true },
      dropShadow: { enabled: true, top: 1, left: 1, blur: 2, opacity: 0.5 }
    },
    stroke: { curve: 'smooth', width: 3 },
    colors: ['#fff'],
    tooltip: {
      theme: 'dark',
      x: { show: false },
      y: {
        title: { formatter: () => 'ARPU' },
        formatter: (val: number) => `€${val.toFixed(2)}`
      }
    },
    annotations: {
      yaxis: [{
        y: 11.5,
        borderColor: '#FFD700',
        strokeDashArray: 4,
        label: {
          text: 'Objetivo €11.5',
          style: { color: '#fff', background: '#FFD700' }
        }
      }]
    }
  },
  chartSeries: [{ name: 'ARPU', data: [...arpuData.value] }],
});

// EchartsGauge - Fantasy
const dataGauge = ref(84);

// Mapa (estático, no cambia en RT)
const dataDownloadsEU = ref([
  { name: "Germany", value: 15000 },
  { name: "France", value: 12000 },
  { name: "Spain", value: 16000 },
  { name: "Italy", value: 9000 },
  { name: "United Kingdom", value: 14000 },
  { name: "Netherlands", value: 8000 },
  { name: "Belgium", value: 7000 },
  { name: "Switzerland", value: 6500 },
  { name: "Austria", value: 6000 },
  { name: "Poland", value: 7500 },
  { name: "Czech Republic", value: 5000 },
  { name: "Portugal", value: 3000 },
  { name: "Sweden", value: 8500 },
  { name: "Norway", value: 4000 },
  { name: "Finland", value: 4200 },
  { name: "Denmark", value: 5500 },
  { name: "Ireland", value: 4800 },
  { name: "Greece", value: 3500 },
  { name: "Hungary", value: 3800 },
  { name: "Romania", value: 3600 },
  { name: "Bulgaria", value: 2500 },
  { name: "Slovakia", value: 3200 },
  { name: "Slovenia", value: 2800 },
  { name: "Croatia", value: 2600 },
  { name: "Estonia", value: 2100 },
  { name: "Latvia", value: 2000 },
  { name: "Lithuania", value: 2300 },
  { name: "Iceland", value: 1500 },
  { name: "Luxembourg", value: 1800 },
  { name: "Malta", value: 1200 },
  { name: "Cyprus", value: 1700 },
  { name: "Ukraine", value: 5000 },
  { name: "Belarus", value: 3000 },
  { name: "Russia", value: 8000 },
  {name: "Turkey", value: 9000 },
  {name: "Serbia", value: 2700},
  {name: "Argentina", value: 4000},
  {name: "Brazil", value: 12000},
  {name: "Mexico", value: 11000},
  {name: "United States of America", value: 20000},
  {name: "Canada", value: 9000},
  {name: "Australia", value: 8500},
  {name: "Japan", value: 13000},
  {name: "China", value: 18000},
  {name: "India", value: 15000},
  {name: "South Korea", value: 7000},
  {name: "South Africa", value: 5000},
  {name: "New Zealand", value: 3000},
]);

// ApexMixedChart
const dataMixedChartSeries = ref([
  {
    name: 'Usuarios activos',
    type: 'column',
    data: [6, 7, 8, 9, 10, 11, 12, 7, 12, 11, 9]
  },
  {
    name: 'Descargas',
    type: 'line',
    data: [5, 6, 7, 8, 10, 12, 13, 6, 12, 10, 8]
  }
]);

/***** 🧠 LÓGICA REALTIME *****/
function addDataRealTime() {

  // 1. SparkLine ARPU: nuevo valor aleatorio entre 9.50 y 12.00
  const newArpu = parseFloat((Math.random() * (12.00 - 9.50) + 9.50).toFixed(2));
  arpuData.value.push(newArpu);
  if (arpuData.value.length > MAX_ARPU_POINTS) {
    arpuData.value = arpuData.value.slice(-MAX_ARPU_POINTS);
  }
  sparkData1.value = {
    ...sparkData1.value,
    value: `€${newArpu.toFixed(2)}`,
    chartSeries: [{ name: 'ARPU', data: [...arpuData.value] }]
  };

  // 2. EchartsGauge: fluctúa alrededor de 80-95%
  dataGauge.value = Math.floor(Math.random() * (95 - 80 + 1)) + 80;

}

/***** 🔄 CICLO DE VIDA *****/
onMounted(() => {
  interval = setInterval(addDataRealTime, UPDATE_INTERVAL);
});

onUnmounted(() => {
  clearInterval(interval);
});
</script>


<style scoped>
ion-row {
  overflow: hidden;
}

ion-col {
  max-height: 100%;
  --ion-grid-column-padding: 10px;
}

.box {
  background: #1E1E1E;
  height: 100%;
  max-height: 100%;
  overflow: hidden;
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

@media (min-width: 992px) {
  ion-grid { height: 100%; }
  .ion-row-1 { height: 20%; max-height: 20%; }
  .ion-row-2 { height: 40%; max-height: 40%; }
  .ion-row-3 { height: 40%; max-height: 40%; }
}

.dashboard-grid {
  display: grid;
  height: 100%;
  gap: 10px;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: 20% 40% 40%;
  grid-template-areas:
    "col1  col1   col1   gauge"
    "grande grande grande gauge"
    "mapa  mapa   spark  spark";
}

.area-col1   { grid-area: col1; }
.area-gauge  { grid-area: gauge; }
.area-grande { grid-area: grande; }
.area-mapa   { grid-area: mapa; }
.area-spark  { grid-area: spark; }

.box {
  background: #1E1E1E;
  border-radius: 5px;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: stretch;
}
</style>