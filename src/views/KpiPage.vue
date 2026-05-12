<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>🎯 KPIs</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">🎯 KPIs</ion-title>
        </ion-toolbar>
      </ion-header>

      <!-- KPIs de Negocio -->
      <h1 class="ion-padding">🏎️ KPIs de Negocio</h1>
      <ion-accordion-group expand="inset" :multiple="true">
        <ion-accordion 
          v-for="item in businessGoals" 
          :key="'business-' + item.id" 
          :value="item.id.toString()"
        >
          <ion-item slot="header">
            <ion-label>{{ item.id }}. {{ item.title }}</ion-label>
          </ion-item>

          <div class="ion-padding" slot="content">
            <p>{{ item.description }}</p>

            <ion-list :inset="true">
              <ion-item v-for="(element, index) in item.smart" :key="index">
                <ion-label>
                  <b>{{ element.letter }}</b> → {{ element.content }}
                </ion-label>
              </ion-item>
            </ion-list>
          </div>
        </ion-accordion>
      </ion-accordion-group>

      <!-- KPIs Técnicos -->
      <h1 class="ion-padding">📈 KPIs Técnicos</h1>
      <ion-accordion-group expand="inset" :multiple="true">
        <ion-accordion 
          v-for="item in technicalGoals" 
          :key="'tech-' + item.id" 
          :value="item.id.toString()"
        >
          <ion-item slot="header">
            <ion-label>{{ item.id }}. {{ item.title }}</ion-label>
          </ion-item>

          <div class="ion-padding" slot="content">
            <p>{{ item.description }}</p>

            <ion-list :inset="true">
              <ion-item v-for="(element, index) in item.smart" :key="index">
                <ion-label>
                  <b>{{ element.letter }}</b> → {{ element.content }}
                </ion-label>
              </ion-item>
            </ion-list>
          </div>
        </ion-accordion>
      </ion-accordion-group>

    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonButtons, IonContent, IonHeader, IonMenuButton,
  IonPage, IonTitle, IonToolbar,
  IonAccordionGroup, IonAccordion,
  IonItem, IonLabel, IonList
} from '@ionic/vue';
import { ref } from 'vue';

interface SmartElement {
  letter: string;
  content: string;
}

interface SmartGoal {
  id: number;
  title: string;
  description: string;
  smart: SmartElement[];
}

/*KPIs DE NEGOCIO */
const businessGoals = ref<SmartGoal[]>([
  { 
    id: 1, title: "Usuarios Activos Mensuales (MAU)", 
    description: "Aumentar los usuarios activos mensuales en un 20% durante la temporada de Formula 1, pasando por ejemplo de 10.000 a 12.000 usuarios, incrementando también las visitas a nuestro sitio y aplicación mediante campañas de adquisición y una mayor distribución de contenido, con el objetivo de fortalecer la base de usuarios antes del lanzamiento de nuevas funcionalidades, todo ello en un plazo de 6 meses.", 
    smart: [ 
      { letter: "S", content: "Aumentar las visitas de nuestro sitio/app durante la temporada de Formula 1" }, 
      { letter: "M", content: "+20% de MAU (ej. de 10,000 a 12,000 usuarios)" }, 
      { letter: "A", content: "Impulsando campañas de adquisición y aumentando la distribución de contenido" }, 
      { letter: "R", content: "Para fortalecer la base de usuarios antes del lanzamiento de nuevas funcionalidades" }, 
      { letter: "T", content: "En los próximos 6 meses" } ] },
  {
    id: 2,
    title: "Conversión a Compra",
    description: "Lograr una tasa de conversión del 5% en la compra de entradas para eventos de Formula 1, mejorando el proceso de compra dentro de la app mediante la optimización del checkout, con el objetivo de aumentar los ingresos generados por los usuarios, en un plazo de 3 meses.",
    smart: [
      { letter: "S", content: "Mejorar compra entradas" },
      { letter: "M", content: "5% conversión" },
      { letter: "A", content: "Optimizar checkout" },
      { letter: "R", content: "Aumentar ingresos" },
      { letter: "T", content: "3 meses" }
    ]
  },
  { id: 3, 
    title: "Ingreso Medio por Usuario (ARPU)", 
    description: "Incrementar el ingreso medio por usuario (ARPU) en un 15%, por ejemplo pasando de 10 € a 11,5 € por usuario, mediante mejoras en el modo fantasy y la monetización de contenidos y venta de entradas relacionadas con la Formula 1, con el objetivo de maximizar el valor de la base de usuarios existente en un plazo de 6 meses.", 
    smart: [ 
      { letter: "S", content: "Incrementar el ingreso medio por usuario(ARPU)" }, 
      { letter: "M", content: "+15% (ej. de €10 a €11.5 por usuario)" }, 
      { letter: "A", content: "Mediante mejoras en el modo fantasy y monetización de contenidos/entradas" }, 
      { letter: "R", content: "Para maximizar el valor de la base de usuarios existente" }, 
      { letter: "T", content: "En 6 meses" } 
    ] 
  },
  { 
    id: 4, 
    title: "Retención (D30)", 
    description: "Alcanzar una tasa de retención de usuarios a 30 días (D30) del 35%, mediante la implementación de notificaciones, contenido personalizado y dinámicas recurrentes dentro de la app de Formula 1, con el objetivo de reducir el churn y aumentar el engagement de los usuarios en un plazo de 4 meses.", 
    smart: [ 
      { letter: "S", content: "Mejorar la retención de usuarios a 30 días" }, 
      { letter: "M", content: "Alcanzar un 35% de retención D30" }, 
      { letter: "A", content: "Implementando notificaciones, contenido personalizado y dinámicas recurrentes" }, 
      { letter: "R", content: "Para reducir churn y aumentar el engagement" }, 
      { letter: "T", content: "En 4 meses" } 
    ] 
  },
  { 
    id: 5, 
    title: "Participación en Fantasy", 
    description: "Conseguir que al menos el 40% de los usuarios activos creen un equipo en el modo fantasy de la app de Formula 1, mejorando el onboarding, los incentivos y la visibilidad de esta funcionalidad, con el objetivo de aumentar el engagement y el tiempo de uso dentro de la aplicación, en un período de 3 meses.", 
    smart: [ 
      { letter: "S", content: "Incrementar la participación en el modo fantasy" }, 
      { letter: "M", content: "Lograr que el 40% de los usuarios activos creen un equipo" }, 
      { letter: "A", content: "Mejorando onboarding, incentivos y visibilidad del modo fantasy" }, 
      { letter: "R", content: "Para aumentar engagement y tiempo en la app" }, 
      { letter: "T", content: "En un período de 3 meses" } 
    ] 
  }
]);

/* KPIs TÉCNICOS */
const technicalGoals = ref<SmartGoal[]>([
  {
    id: 1,
    title: "Tiempo de carga",
    description: "Reducir el tiempo de carga de las pantallas clave de la app, como resultados y datos de pilotos de Formula 1, hasta alcanzar un tiempo promedio inferior a 2 segundos, mediante la optimización de assets, el uso de caching y la mejora del rendimiento del backend, con el objetivo de mejorar la experiencia de usuario y reducir el abandono, en un plazo de 2 meses.",
    smart: [
      { letter: "S", content: "Reducir el tiempo de carga de pantallas clave (resultados, pilotos)" },
      { letter: "M", content: "< 2 segundos de carga promedio" },
      { letter: "A", content: "Optimizando assets, caching y rendimiento backend" },
      { letter: "R", content: "Para mejorar la experiencia de usuario y reducir abandono" },
      { letter: "T", content: "En 2 meses" }
    ]
  },
  {
    id: 2,
    title: "Tasa de Errores (Crash Rate)",
    description: "Mantener la tasa de fallos de la aplicación por debajo del 1% de las sesiones mensuales durante toda la temporada de Formula 1, mediante un monitoreo continuo, testing constante y la priorización de corrección de errores, con el objetivo de garantizar la estabilidad del sistema y la confianza de los usuarios.",
    smart: [
      { letter: "S", content: "Reducir la tasa de fallos de la aplicación" },
      { letter: "M", content: "< 1% de sesiones con errores" },
      { letter: "A", content: "Mediante monitoreo continuo, testing y fixes prioritarios" },
      { letter: "R", content: "para garantizar estabilidad y confianza del usuario" },
      { letter: "T", content: "Durante toda la temporada" }
    ]
  },
  {
    id: 3,
    title: "Latencia de Resultados en Vivo",
    description: "Actualizar los resultados de carrera en la app en un máximo de 5 segundos desde el evento real durante el 100% de los Grandes Premios de la temporada de Formula 1, optimizando los pipelines de datos en tiempo real, con el objetivo de ofrecer una experiencia competitiva y fiable en el seguimiento en vivo de las carreras durante toda la temporada.",
    smart: [
      { letter: "S", content: "Mejorar la velocidad de actualización de resultados en vivo" },
      { letter: "M", content: "≤ 5 segundos de latencia desde el evento real" },
      { letter: "A", content: "Optimizando pipelines de datos en tiempo real" },
      { letter: "R", content: "Para ofrecer una experiencia competitiva en seguimiento de carreras" },
      { letter: "T", content: "Durante toda la temporada" }
    ]
  },
  {
    id: 4,
    title: "Disponibilidad (Uptime)",
    description: "Garantizar una disponibilidad del sistema del 99.9% mensual durante toda la temporada de Formula 1, mediante una infraestructura redundante y un monitoreo proactivo constante, con el objetivo de evitar caídas en momentos críticos como los fines de semana de carrera y asegurar una experiencia estable y continua para los usuarios durante todo el periodo competitivo.",
    smart: [
      { letter: "S", content: "Garantizar alta disponibilidad del sistema" },
      { letter: "M", content: "99.9% de uptime mensual" },
      { letter: "A", content: "Mediante infraestructura redundante y monitoreo proactivo" },
      { letter: "R", content: "Para evitar caídas en momentos críticos (carreras)" },
      { letter: "T", content: "Durante toda la temporada" }
    ]
  },
  {
    id: 5,
    title: "Cálculo de Puntos Fantasy",
    description: "Procesar y reflejar los puntos del modo fantasy en menos de 10 segundos tras finalizar cada carrera de la temporada de Formula 1, incluyendo el rendimiento de pilotos como Lewis Hamilton, mediante la mejora del procesamiento de datos y la automatización de los cálculos, con el objetivo de ofrecer un feedback inmediato al usuario y mantener una experiencia fluida y competitiva en cada carrera durante toda la temporada.",
    smart: [
      { letter: "S", content: "Optimizar el cálculo de puntos del modo fantasy incluyendo pilotos como Lewis Hamilton" },
      { letter: "M", content: "≤ 10 segundos tras finalizar la carrera" },
      { letter: "A", content: "Mejorando procesamiento de datos y automatización" },
      { letter: "R", content: "Para ofrecer feedback inmediato al usuario" },
      { letter: "T", content: "En cada carrera durante la temporada" }
    ]
  },
]);
</script>

<style scoped>
ion-accordion.accordion-collapsing ion-item[slot='header'],
ion-accordion.accordion-collapsed ion-item[slot='header'] {
  --background: var(--ion-color-light);
  --color: var(--ion-color-light-contrast);
}

ion-accordion.accordion-expanding ion-item[slot='header'],
ion-accordion.accordion-expanded ion-item[slot='header'] {
  --background: rgba(var(--ion-color-primary-rgb), 0.14);
  color: var(--ion-color-primary);
}
</style>