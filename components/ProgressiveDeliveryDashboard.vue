<script setup lang="ts">
import { ref, computed, watch } from 'vue';
import { Chart, registerables } from 'chart.js';
import { onSlideEnter, onSlideLeave, useSlideContext } from '@slidev/client';

Chart.register(...registerables);

const { $clicks } = useSlideContext();

// Canvas refs
const trafficChartCanvas = ref<HTMLCanvasElement | null>(null);
const errorChartCanvas = ref<HTMLCanvasElement | null>(null);
const latencyChartCanvas = ref<HTMLCanvasElement | null>(null);

// Chart instances - stored in refs for reactivity
const trafficChart = ref<Chart | null>(null);
const errorChart = ref<Chart | null>(null);
const latencyChart = ref<Chart | null>(null);

// Rollout stages based on clicks
// Slide starts at click 0 = 1% rollout
// Click 1 = 10% rollout
// Click 2 = 50% with issue
// Click 3 = rollback
const rolloutStage = computed(() => {
  const clicks = $clicks.value ?? 0;
  if (clicks >= 3) return 'rollback';
  if (clicks >= 2) return 'issue';
  if (clicks >= 1) return 'healthy-10';
  // Click 0 (initial state)
  return 'healthy-1';
});

const rolloutPercentage = computed(() => {
  if (rolloutStage.value === 'rollback') return 0;
  if (rolloutStage.value === 'issue') return 50;
  if (rolloutStage.value === 'healthy-10') return 10;
  return 1;
});

const controlPercentage = computed(() => 100 - rolloutPercentage.value);

// Generate hardcoded data for consistency
const generateErrorData = (stage: string) => {
  const baseControl = Array(20).fill(null).map(() => 0.08 + Math.random() * 0.04); // 0.08-0.12%
  const baseTreatment = Array(20).fill(null).map(() => 0.09 + Math.random() * 0.05); // 0.09-0.14%

  if (stage === 'issue') {
    // Treatment variant degrades at 50% rollout
    return {
      control: baseControl,
      treatment: baseTreatment.map((v, i) => i > 12 ? 13 + Math.random() * 4 : v), // Spike to 13-17%
    };
  }

  if (stage === 'rollback') {
    // After rollback: show spike then recovery as treatment traffic goes to 0%
    return {
      control: baseControl,
      treatment: baseTreatment.map((v, i) => {
        if (i <= 12) return v; // Normal before issue
        if (i <= 16) return 13 + Math.random() * 4; // Spike during issue
        return 0; // After rollback, no treatment traffic
      }),
    };
  }

  return { control: baseControl, treatment: baseTreatment };
};

const generateLatencyData = (stage: string) => {
  const baseControl = Array(20).fill(null).map(() => 175 + Math.random() * 15); // 175-190ms
  const baseTreatment = Array(20).fill(null).map(() => 180 + Math.random() * 18); // 180-198ms

  if (stage === 'issue') {
    // Treatment variant degrades
    return {
      control: baseControl,
      treatment: baseTreatment.map((v, i) => i > 12 ? 1100 + Math.random() * 200 : v), // Spike to 1100-1300ms
    };
  }

  if (stage === 'rollback') {
    // After rollback: show spike then recovery as treatment traffic goes to 0%
    return {
      control: baseControl,
      treatment: baseTreatment.map((v, i) => {
        if (i <= 12) return v; // Normal before issue
        if (i <= 16) return 1100 + Math.random() * 200; // Spike during issue
        return 0; // After rollback, no treatment traffic
      }),
    };
  }

  return { control: baseControl, treatment: baseTreatment };
};

const timeLabels = Array(20).fill(null).map((_, i) => {
  const mins = i * 2;
  return `${mins}m`;
});

// Destroy all charts
const destroyCharts = () => {
  if (trafficChart.value) {
    trafficChart.value.destroy();
    trafficChart.value = null;
  }
  if (errorChart.value) {
    errorChart.value.destroy();
    errorChart.value = null;
  }
  if (latencyChart.value) {
    latencyChart.value.destroy();
    latencyChart.value = null;
  }
};

// Create or recreate all charts based on current rollout stage
const createCharts = () => {
  // Destroy existing charts first
  destroyCharts();

  // Check if canvases are available
  if (!trafficChartCanvas.value || !errorChartCanvas.value || !latencyChartCanvas.value) {
    console.warn('Canvas refs not available');
    return;
  }

  // Get current stage data
  const currentStage = rolloutStage.value;
  const errorData = generateErrorData(currentStage);
  const latencyData = generateLatencyData(currentStage);

  // Create traffic chart
  trafficChart.value = new Chart(trafficChartCanvas.value, {
    type: 'doughnut',
    data: {
      labels: ['Control', 'Treatment'],
      datasets: [{
        data: [controlPercentage.value, rolloutPercentage.value],
        backgroundColor: [
          'rgba(34, 197, 94, 0.8)', // Green for control
          currentStage === 'issue' || currentStage === 'rollback'
            ? 'rgba(239, 68, 68, 0.8)' // Red when issue
            : 'rgba(59, 130, 246, 0.8)', // Blue normally
        ],
        borderColor: [
          'rgba(34, 197, 94, 1)',
          currentStage === 'issue' || currentStage === 'rollback'
            ? 'rgba(239, 68, 68, 1)'
            : 'rgba(59, 130, 246, 1)',
        ],
        borderWidth: 2,
      }],
    },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            display: true,
            position: 'bottom',
            labels: {
              color: 'rgba(255, 255, 255, 0.9)',
              font: { size: 11 },
              padding: 8,
            },
          },
          tooltip: {
            callbacks: {
              label: (context) => {
                return `${context.label}: ${context.parsed}%`;
              },
            },
          },
        },
      },
    });

  // Create error chart
  errorChart.value = new Chart(errorChartCanvas.value, {
      type: 'line',
      data: {
        labels: timeLabels,
        datasets: [
          {
            label: 'Control',
            data: errorData.control,
            borderColor: 'rgba(34, 197, 94, 1)',
            backgroundColor: 'rgba(34, 197, 94, 0.1)',
            borderWidth: 2,
            pointRadius: 0,
            tension: 0.3,
          },
          {
            label: 'Treatment',
            data: errorData.treatment,
            borderColor: currentStage === 'issue' || currentStage === 'rollback'
              ? 'rgba(239, 68, 68, 1)'
              : 'rgba(59, 130, 246, 1)',
            backgroundColor: currentStage === 'issue' || currentStage === 'rollback'
              ? 'rgba(239, 68, 68, 0.1)'
              : 'rgba(59, 130, 246, 0.1)',
            borderWidth: 2,
            pointRadius: 0,
            tension: 0.3,
          },
        ],
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          x: {
            display: true,
            grid: { color: 'rgba(255, 255, 255, 0.05)' },
            ticks: {
              color: 'rgba(255, 255, 255, 0.6)',
              font: { size: 9 },
              maxTicksLimit: 6,
            },
          },
          y: {
            display: true,
            grid: { color: 'rgba(255, 255, 255, 0.05)' },
            ticks: {
              color: 'rgba(255, 255, 255, 0.6)',
              font: { size: 9 },
              callback: (value) => `${value}%`,
            },
            max: currentStage === 'issue' || currentStage === 'rollback' ? 20 : 0.5,
          },
        },
        plugins: {
          legend: {
            display: true,
            position: 'top',
            labels: {
              color: 'rgba(255, 255, 255, 0.9)',
              font: { size: 10 },
              padding: 6,
              usePointStyle: true,
            },
          },
          tooltip: {
            callbacks: {
              label: (context) => {
                return `${context.dataset.label}: ${context?.parsed?.y?.toFixed(2)}%`;
              },
            },
          },
        },
      },
    });

  // Create latency chart
  latencyChart.value = new Chart(latencyChartCanvas.value, {
      type: 'line',
      data: {
        labels: timeLabels,
        datasets: [
          {
            label: 'Control',
            data: latencyData.control,
            borderColor: 'rgba(34, 197, 94, 1)',
            backgroundColor: 'rgba(34, 197, 94, 0.1)',
            borderWidth: 2,
            pointRadius: 0,
            tension: 0.3,
          },
          {
            label: 'Treatment',
            data: latencyData.treatment,
            borderColor: currentStage === 'issue' || currentStage === 'rollback'
              ? 'rgba(239, 68, 68, 1)'
              : 'rgba(59, 130, 246, 1)',
            backgroundColor: currentStage === 'issue' || currentStage === 'rollback'
              ? 'rgba(239, 68, 68, 0.1)'
              : 'rgba(59, 130, 246, 0.1)',
            borderWidth: 2,
            pointRadius: 0,
            tension: 0.3,
          },
        ],
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          x: {
            display: true,
            grid: { color: 'rgba(255, 255, 255, 0.05)' },
            ticks: {
              color: 'rgba(255, 255, 255, 0.6)',
              font: { size: 9 },
              maxTicksLimit: 6,
            },
          },
          y: {
            display: true,
            grid: { color: 'rgba(255, 255, 255, 0.05)' },
            ticks: {
              color: 'rgba(255, 255, 255, 0.6)',
              font: { size: 9 },
              callback: (value) => `${value}ms`,
            },
            max: currentStage === 'issue' || currentStage === 'rollback' ? 1400 : 250,
          },
        },
        plugins: {
          legend: {
            display: true,
            position: 'top',
            labels: {
              color: 'rgba(255, 255, 255, 0.9)',
              font: { size: 10 },
              padding: 6,
              usePointStyle: true,
            },
          },
          tooltip: {
            callbacks: {
              label: (context) => {
                return `${context.dataset.label}: ${context?.parsed?.y?.toFixed(0)}ms`;
              },
            },
          },
        },
      },
    });
};

// Watch for rollout stage changes and recreate charts
watch(rolloutStage, () => {
  // Recreate all charts with new data when stage changes
  if (trafficChartCanvas.value && errorChartCanvas.value && latencyChartCanvas.value) {
    createCharts();
  }
});

// Lifecycle hooks
onSlideEnter(() => {
  // Wait for DOM to be ready, then create charts
  setTimeout(() => {
    createCharts();
  }, 100);
});

onSlideLeave(() => {
  // Destroy all charts when leaving slide
  destroyCharts();
});
</script>

<template>
  <div class="dashboard-container">
    <!-- Rollout Status Header -->
    <div class="dashboard-header">
      <div flex items-center justify-between mb-2>
        <div flex items-center gap-3>
          <div class="i-carbon:flag text-purple-400 text-xl" />
          <div>
            <span class="flag-name">new-checkout-flow</span>
            <span class="service-name">@ checkout-service</span>
          </div>
        </div>
        <div class="rollout-badge" :class="{
          'healthy': rolloutStage === 'healthy-1' || rolloutStage === 'healthy-10',
          'issue': rolloutStage === 'issue',
          'rollback': rolloutStage === 'rollback'
        }">
          <span v-if="rolloutStage === 'rollback'">🔄 Rolled Back</span>
          <span v-else-if="rolloutStage === 'issue'">🚨 Issue Detected</span>
          <span v-else>✅ Healthy</span>
        </div>
      </div>

      <div class="progress-bar-container">
        <div class="progress-bar">
          <div
            class="progress-fill"
            :class="{ 'progress-issue': rolloutStage === 'issue', 'progress-rollback': rolloutStage === 'rollback' }"
            :style="{ width: `${rolloutPercentage}%` }"
          ></div>
        </div>
        <div class="progress-label">
          {{ rolloutPercentage }}% rollout ({{ controlPercentage }}% control / {{ rolloutPercentage }}% treatment)
        </div>
      </div>
    </div>

    <!-- Metrics Grid -->
    <div class="metrics-grid">
      <!-- Traffic Split -->
      <div class="metric-card">
        <div class="metric-header">
          <div class="i-carbon:chart-pie text-blue-400 text-base" />
          <span class="metric-title">Traffic Split</span>
        </div>
        <div class="chart-container" style="height: 140px; position: relative;">
          <canvas ref="trafficChartCanvas" style="max-height: 140px;"></canvas>
        </div>
      </div>

      <!-- Error Rate -->
      <div class="metric-card" :class="{ 'card-alert': rolloutStage === 'issue' }">
        <div class="metric-header">
          <div class="i-carbon:warning-alt text-red-400 text-base" />
          <span class="metric-title">Error Rate</span>
        </div>
        <div class="chart-container" style="height: 140px; position: relative;">
          <canvas ref="errorChartCanvas" style="max-height: 140px;"></canvas>
        </div>
      </div>

      <!-- P95 Latency -->
      <div class="metric-card" :class="{ 'card-alert': rolloutStage === 'issue' }">
        <div class="metric-header">
          <div class="i-carbon:time text-orange-400 text-base" />
          <span class="metric-title">P95 Latency</span>
        </div>
        <div class="chart-container" style="height: 140px; position: relative;">
          <canvas ref="latencyChartCanvas" style="max-height: 140px;"></canvas>
        </div>
      </div>
    </div>

    <!-- Callout Box -->
    <div v-if="rolloutStage === 'issue'" class="callout-box issue-callout">
      <div class="callout-header">
        <div class="i-carbon:bullhorn text-purple-300 text-xl" />
        <span class="font-bold">🎯 Feature Flag Observability Reveals:</span>
      </div>
      <div class="callout-content">
        <div class="callout-item">
          <div class="i-carbon:warning-alt text-red-400" />
          <span><strong>Treatment variant</strong> is causing degradation:</span>
        </div>
        <div class="callout-stats">
          <div>• 150x higher error rate (0.1% → 15%)</div>
          <div>• 6.6x slower response time (180ms → 1200ms)</div>
        </div>
        <div class="callout-item success">
          <div class="i-carbon:checkmark-filled text-green-400" />
          <span><strong>Control variant</strong> remains healthy ✅</span>
        </div>
      </div>
    </div>

    <div v-if="rolloutStage === 'rollback'" class="callout-box rollback-callout">
      <div class="callout-header">
        <div class="i-carbon:checkmark-filled text-green-300 text-xl" />
        <span class="font-bold">✅ Rollback Triggered - Treatment Variant Disabled</span>
      </div>
      <div class="callout-content">
        <div class="comparison-grid">
          <div class="comparison-item without">
            <div class="comparison-label">❌ Without Flag Observability:</div>
            <div class="comparison-text">Hours debugging aggregate metrics, unclear which change caused the issue</div>
          </div>
          <div class="comparison-item with">
            <div class="comparison-label">✅ With Flag Observability:</div>
            <div class="comparison-text">Instant variant identification & rollback in seconds</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.dashboard-container {
  width: 100%;
  max-width: 840px;
  margin: 0 auto;
}

.dashboard-header {
  background: linear-gradient(135deg, rgba(54, 56, 85, 0.82), rgba(26, 28, 44, 0.92));
  border: 1.5px solid rgba(141, 141, 255, 0.35);
  border-radius: 8px;
  padding: 0.625rem;
  margin-bottom: 0.625rem;
  box-shadow: 0 0 20px rgba(109, 118, 255, 0.25);
}

.flag-name {
  font-family: 'Fira Code', monospace;
  font-size: 0.875rem;
  color: rgba(141, 141, 255, 1);
  font-weight: 600;
  margin-right: 0.5rem;
}

.service-name {
  font-family: 'Fira Code', monospace;
  font-size: 0.75rem;
  color: rgba(255, 255, 255, 0.7);
}

.rollout-badge {
  padding: 0.375rem 0.75rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
  transition: all 0.3s ease;
  border: 1px solid transparent; /* Always have border to prevent size shift */
}

.rollout-badge.healthy {
  background: rgba(34, 197, 94, 0.2);
  border-color: rgba(34, 197, 94, 0.4);
  color: #4ade80;
}

.rollout-badge.issue {
  background: rgba(239, 68, 68, 0.2);
  border-color: rgba(239, 68, 68, 0.4);
  color: #f87171;
  animation: pulse-glow 2s ease-in-out infinite;
}

.rollout-badge.rollback {
  background: rgba(59, 130, 246, 0.2);
  border-color: rgba(59, 130, 246, 0.4);
  color: #60a5fa;
}

@keyframes pulse-glow {
  0%, 100% { box-shadow: 0 0 10px rgba(239, 68, 68, 0.3); }
  50% { box-shadow: 0 0 20px rgba(239, 68, 68, 0.6); }
}

.progress-bar-container {
  margin-top: 0.75rem;
}

.progress-bar {
  width: 100%;
  height: 6px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 3px;
  overflow: hidden;
  margin-bottom: 0.375rem;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, rgba(59, 130, 246, 0.8), rgba(141, 141, 255, 0.9));
  border-radius: 3px;
  transition: width 0.5s ease, background 0.3s ease;
}

.progress-fill.progress-issue {
  background: linear-gradient(90deg, rgba(239, 68, 68, 0.8), rgba(220, 38, 38, 0.9));
}

.progress-fill.progress-rollback {
  background: linear-gradient(90deg, rgba(59, 130, 246, 0.6), rgba(96, 165, 250, 0.7));
}

.progress-label {
  font-size: 0.6875rem;
  color: rgba(255, 255, 255, 0.7);
  text-align: center;
}

.metrics-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 0.5rem;
  margin-bottom: 0.5rem;
  width: 100%;
}

.metric-card {
  background: linear-gradient(135deg, rgba(54, 56, 85, 0.5), rgba(24, 26, 38, 0.45));
  border: 1.5px solid rgba(141, 141, 255, 0.25);
  border-radius: 8px;
  padding: 0.5rem;
  transition: all 0.3s ease;
  box-shadow: 0 4px 16px 0 rgba(60, 66, 110, 0.25);
  min-width: 0;
  overflow: hidden;
}

.metric-card.card-alert {
  border-color: rgba(239, 68, 68, 0.4);
  box-shadow: 0 4px 20px rgba(239, 68, 68, 0.3);
}

.metric-header {
  display: flex;
  align-items: center;
  gap: 0.375rem;
  margin-bottom: 0.5rem;
}

.metric-title {
  font-size: 0.75rem;
  font-weight: 600;
  color: rgba(255, 255, 255, 0.9);
}

.chart-container {
  position: relative;
  width: 100%;
  max-width: 100%;
  overflow: hidden;
}

.chart-container canvas {
  max-width: 100%;
  height: auto !important;
}

.callout-box {
  border-radius: 6px;
  padding: 0.5rem 0.625rem;
  margin-top: 0.5rem;
  animation: slideIn 0.4s ease-out;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.issue-callout {
  background: linear-gradient(135deg, rgba(141, 141, 255, 0.18), rgba(93, 93, 255, 0.12));
  border: 1.5px solid rgba(141, 141, 255, 0.4);
  box-shadow: 0 0 25px rgba(109, 118, 255, 0.35);
}

.rollback-callout {
  background: linear-gradient(135deg, rgba(34, 197, 94, 0.15), rgba(21, 128, 61, 0.12));
  border: 1.5px solid rgba(34, 197, 94, 0.4);
  box-shadow: 0 0 25px rgba(34, 197, 94, 0.3);
}

.callout-header {
  display: flex;
  align-items: center;
  gap: 0.375rem;
  margin-bottom: 0.375rem;
  font-size: 0.75rem;
  color: rgba(255, 255, 255, 0.95);
}

.callout-content {
  font-size: 0.6875rem;
  color: rgba(255, 255, 255, 0.9);
}

.callout-item {
  display: flex;
  align-items: center;
  gap: 0.375rem;
  margin-bottom: 0.25rem;
}

.callout-item.success {
  margin-top: 0.375rem;
  padding-top: 0.375rem;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.callout-stats {
  margin-left: 1.5rem;
  margin-top: 0.125rem;
  font-size: 0.625rem;
  opacity: 0.9;
  line-height: 1.4;
}

.comparison-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.5rem;
}

.comparison-item {
  padding: 0.5rem;
  border-radius: 4px;
}

.comparison-item.without {
  background: rgba(239, 68, 68, 0.1);
  border: 1px solid rgba(239, 68, 68, 0.3);
}

.comparison-item.with {
  background: rgba(34, 197, 94, 0.1);
  border: 1px solid rgba(34, 197, 94, 0.3);
}

.comparison-label {
  font-weight: 600;
  font-size: 0.625rem;
  margin-bottom: 0.125rem;
}

.comparison-text {
  font-size: 0.5625rem;
  opacity: 0.9;
  line-height: 1.25;
}
</style>
