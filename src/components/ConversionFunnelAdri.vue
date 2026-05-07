<template>
  <div class="donut-wrapper">

    <!-- Cabecera -->
    <div class="donut-header">
      <span class="donut-title">Conversión a Compra</span>
      <span class="donut-badge" :class="currentConversion >= target ? 'badge-ok' : 'badge-warn'">
        {{ currentConversion.toFixed(2) }}% / {{ target }}% obj.
      </span>
    </div>

    <!-- SVG Donut -->
    <svg viewBox="0 0 220 220" class="donut-svg" xmlns="http://www.w3.org/2000/svg">
      <defs>
        <linearGradient id="grad-conversion" x1="0%" y1="0%" x2="100%" y2="100%">
          <stop offset="0%" stop-color="#3b82f6" />
          <stop offset="100%" stop-color="#10b981" />
        </linearGradient>
        <linearGradient id="grad-target" x1="0%" y1="0%" x2="100%" y2="100%">
          <stop offset="0%" stop-color="#FFD700" stop-opacity="0.3"/>
          <stop offset="100%" stop-color="#FFD700" stop-opacity="0.6"/>
        </linearGradient>
        <filter id="glow">
          <feGaussianBlur stdDeviation="2.5" result="coloredBlur"/>
          <feMerge>
            <feMergeNode in="coloredBlur"/>
            <feMergeNode in="SourceGraphic"/>
          </feMerge>
        </filter>
      </defs>

      <!-- Fondo -->
      <circle cx="110" cy="110" r="80" fill="none" stroke="#2a2a2a" stroke-width="26"/>

      <!-- Objetivo -->
      <circle
        cx="110" cy="110" r="80"
        fill="none"
        stroke="url(#grad-target)"
        stroke-width="26"
        stroke-dasharray="25.13 502.65"
        stroke-dashoffset="125.66"
        stroke-linecap="round"
      />

      <!-- Conversión -->
      <circle
        cx="110" cy="110" r="80"
        fill="none"
        stroke="url(#grad-conversion)"
        stroke-width="26"
        :stroke-dasharray="`${conversionArc} 502.65`"
        stroke-dashoffset="125.66"
        stroke-linecap="round"
        filter="url(#glow)"
        class="arc-transition"
      />

      <!-- Texto -->
      <text x="110" y="100" class="center-value">
        {{ currentConversion.toFixed(2) }}%
      </text>
      <text x="110" y="120" class="center-label">conversión</text>
      <text x="110" y="138" class="center-sub">
        {{ compras.toLocaleString() }} compras
      </text>

      <!-- Marcador -->
      <line
        :x1="targetMarker.x1" :y1="targetMarker.y1"
        :x2="targetMarker.x2" :y2="targetMarker.y2"
        stroke="#FFD700" stroke-width="2"
        stroke-linecap="round"
      />
      <text :x="targetMarker.lx" :y="targetMarker.ly" class="marker-label">5%</text>

    </svg>

    <!-- Leyenda -->
    <div class="legend-row">
      <div class="legend-item">
        <span class="legend-dot grad"></span>
        <span>Conversión actual</span>
      </div>
      <div class="legend-item">
        <span class="legend-dot target"></span>
        <span>Objetivo (5%)</span>
      </div>
      <div class="legend-item">
        <span class="legend-dot bg"></span>
        <span>Restante</span>
      </div>
    </div>

    <!-- KPIs -->
    <div class="kpi-row">
      <div class="kpi-item">
        <span class="kpi-val">{{ visitas.toLocaleString() }}</span>
        <span class="kpi-lbl">👁️ Visitas</span>
      </div>
      <div class="kpi-divider"></div>
      <div class="kpi-item">
        <span class="kpi-val">{{ compras.toLocaleString() }}</span>
        <span class="kpi-lbl">✅ Compras</span>
      </div>
      <div class="kpi-divider"></div>
      <div class="kpi-item">
        <span class="kpi-val trend" :class="trend >= 0 ? 'up' : 'down'">
          {{ trend >= 0 ? '▲' : '▼' }} {{ Math.abs(trend).toFixed(2) }}%
        </span>
        <span class="kpi-lbl">Tendencia</span>
      </div>
    </div>

  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue';

const target = 5;
const CIRCUMFERENCE = 2 * Math.PI * 80;
const MAX_PERCENT = 20;

const visitas = ref(10000);
const compras = ref(480);
const prevConversion = ref(4.80);

const currentConversion = computed(() => (compras.value / visitas.value) * 100);
const trend = computed(() => currentConversion.value - prevConversion.value);

const conversionArc = computed(() => {
  const ratio = Math.min(currentConversion.value / MAX_PERCENT, 1);
  return ratio * CIRCUMFERENCE;
});

const targetMarker = computed(() => {
  const angle = ((target / MAX_PERCENT) * 360 - 90) * (Math.PI / 180);
  const r1 = 66, r2 = 94;
  return {
    x1: 110 + r1 * Math.cos(angle),
    y1: 110 + r1 * Math.sin(angle),
    x2: 110 + r2 * Math.cos(angle),
    y2: 110 + r2 * Math.sin(angle),
    lx: 110 + 105 * Math.cos(angle),
    ly: 110 + 105 * Math.sin(angle),
  };
});

let interval: ReturnType<typeof setInterval>;

function updateData() {
  prevConversion.value = currentConversion.value;
  compras.value = Math.floor(visitas.value * (0.03 + Math.random() * 0.05));
}

onMounted(() => { interval = setInterval(updateData, 2000); });
onUnmounted(() => { clearInterval(interval); });
</script>

<style scoped>
.donut-wrapper {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
  padding: 12px 16px;
  gap: 6px;
  box-sizing: border-box;
}

/* Header */
.donut-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.donut-title {
  font-size: 13px;
  font-weight: 700;
  color: #e0e0e0;
}
.donut-badge {
  font-size: 11px;
  font-weight: 600;
  padding: 2px 8px;
  border-radius: 20px;
}
.badge-ok   { background: #10b98133; color: #10b981; }
.badge-warn { background: #f9731633; color: #f97316; }

/* SVG */
.donut-svg {
  width: 100%;
  flex: 1;
}

.arc-transition {
  transition: stroke-dasharray 0.8s cubic-bezier(0.4, 0, 0.2, 1);
}

/* Text */
.center-value {
  font-size: 24px;
  font-weight: 800;
  fill: #fff;
  text-anchor: middle;
}
.center-label {
  font-size: 11px;
  fill: #888;
  text-anchor: middle;
}
.center-sub {
  font-size: 10px;
  fill: #666;
  text-anchor: middle;
}
.marker-label {
  font-size: 10px;
  fill: #FFD700;
  text-anchor: middle;
}

/* Legend */
.legend-row {
  display: flex;
  justify-content: center;
  gap: 12px;
  font-size: 10px;
  color: #888;
}
.legend-item {
  display: flex;
  align-items: center;
  gap: 4px;
}
.legend-dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
}
.legend-dot.grad {
  background: linear-gradient(135deg, #3b82f6, #10b981);
}
.legend-dot.target {
  background: #FFD700;
  opacity: 0.7;
}
.legend-dot.bg {
  background: #2a2a2a;
}

/* KPIs */
.kpi-row {
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding: 6px 0;
  border-top: 1px solid #2a2a2a;
  border-bottom: 1px solid #2a2a2a;
}
.kpi-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.kpi-val {
  font-size: 13px;
  font-weight: 700;
  color: #fff;
}
.kpi-lbl {
  font-size: 10px;
  color: #666;
}
.kpi-divider {
  width: 1px;
  height: 28px;
  background: #2a2a2a;
}
.trend.up { color: #10b981; }
.trend.down { color: #ef4444; }
</style>