<template>
  <div class="problem-card">
    <!-- Header -->
    <div class="problem-header">
      <div class="problem-info">
        <div class="problem-title">
          <span class="severity-badge error">Error</span>
          <span class="problem-name">Failure rate increase</span>
        </div>
        <div class="problem-meta">{{ timestamp }}</div>
      </div>
    </div>

    <!-- Description -->
    <div class="problem-description">
      The error rate increased to <span class="highlight">{{ errorRate }}%</span>. 
      All dynamic requests have increased failure rates.
    </div>

    <!-- Chart -->
    <div class="chart-container">
      <div class="alert-overlay">
        <div class="alert-box"></div>
      </div>
      <svg class="chart" viewBox="0 0 1000 200" preserveAspectRatio="none">
        <!-- Grid lines -->
        <line v-for="i in 6" :key="i" 
          :x1="0" :y1="i * 40" :x2="1000" :y2="i * 40" 
          stroke="rgba(255,255,255,0.1)" stroke-width="1"/>

        
        <!-- Spike data line -->
        <polyline
          :points="spikePoints"
          fill="none"
          stroke="#4a9eff"
          stroke-width="2"
          stroke-linejoin="round"
        />
        
        <!-- Data points -->
        <circle v-for="(point, i) in dataPoints" :key="`point-${i}`"
          :cx="point.x" :cy="point.y"
          r="3" fill="#4a9eff"/>
      </svg>
      
      <!-- X-axis time labels -->
      <div class="time-labels">
        <span v-for="time in timeLabels" :key="time">{{ time }}</span>
      </div>
    </div>

    <!-- Affected Service -->
    <div class="affected-service">
      <div class="service-label">Affected service</div>
      <div class="service-info">
        <div class="service-icon">
          <div i-logos:java />
        </div>
        <span class="service-name">{{ serviceName }}</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps({
  serviceName: {
    type: String,
    default: 'checkout-service'
  },
  errorRate: {
    type: Number,
    default: 7.14
  },
  timestamp: {
    type: String,
    default: 'Oct 15, 2025, 2:35 PM'
  }
})

const dataPoints = [
  { x: 0, y: 195 },
  { x: 50, y: 195 },
  { x: 100, y: 194 },
  { x: 150, y: 195 },
  { x: 200, y: 195 },
  { x: 250, y: 195 },
  { x: 300, y: 195 },
  { x: 350, y: 195 },
  { x: 400, y: 140 },
  { x: 450, y: 120 },
  { x: 500, y: 123 },
  { x: 550, y: 80 },
  { x: 600, y: 50 },
  { x: 650, y: 75 },
  { x: 700, y: 47 },
  { x: 750, y: 60 },
  { x: 800, y: 37 },
  { x: 850, y: 42 },
  { x: 900, y: 60 },
  { x: 950, y: 58 }
]

const spikePoints = computed(() => {
  return dataPoints.map(p => `${p.x},${p.y}`).join(' ')
})

const timeLabels = ['2:20', '2:25', '2:30', '2:35', '2:40', '2:45', '2:50', '2:55', '3:00']
</script>

<style scoped>
.problem-card {
  background: linear-gradient(180deg, #1a1a2e 0%, #16213e 100%);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 6px;
  padding: 0.75rem;
  color: #fff;
  font-family: system-ui, -apple-system, sans-serif;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
}

.problem-header {
  display: flex;
  align-items: flex-start;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
}

.problem-icon {
  font-size: 1.2rem;
  margin-top: 0.1rem;
}

.problem-info {
  flex: 1;
}

.problem-title {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.15rem;
}

.severity-badge {
  padding: 0.15rem 0.4rem;
  border-radius: 3px;
  font-size: 0.65rem;
  font-weight: 600;
  text-transform: uppercase;
}

.severity-badge.error {
  background: rgba(239, 68, 68, 0.2);
  color: #f87171;
  border: 1px solid rgba(239, 68, 68, 0.3);
}

.problem-name {
  font-size: 0.9rem;
  font-weight: 500;
}

.problem-meta {
  font-size: 0.7rem;
  color: rgba(255, 255, 255, 0.6);
}

.analyze-btn {
  display: flex;
  align-items: center;
  gap: 0.4rem;
  background: rgba(74, 158, 255, 0.15);
  color: #4a9eff;
  border: 1px solid rgba(74, 158, 255, 0.3);
  border-radius: 4px;
  padding: 0.35rem 0.75rem;
  font-size: 0.75rem;
  cursor: pointer;
  transition: all 0.2s;
}

.analyze-btn:hover {
  background: rgba(74, 158, 255, 0.25);
}

.btn-icon {
  font-size: 0.85rem;
}

.problem-description {
  font-size: 0.8rem;
  color: rgba(255, 255, 255, 0.85);
  margin-bottom: 0.75rem;
  line-height: 1.4;
}

.highlight {
  color: #f87171;
  font-weight: 600;
}

.chart-container {
  position: relative;
  background: rgba(0, 0, 0, 0.3);
  border-radius: 4px;
  padding: 0.5rem 0.5rem 1.25rem 0.5rem;
  margin-bottom: 0.5rem;
}

.alert-overlay {
  position: absolute;
  top: 1rem;
  left: 36%;
  right: 0.5rem;
  height: 60%;
  pointer-events: none;
}

.alert-box {
  width: 100%;
  height: 100%;
  background: rgba(239, 68, 68, 0.15);
  border: 1px solid rgba(239, 68, 68, 0.3);
  border-radius: 3px;
}

.chart {
  width: 100%;
  height: 100px;
  display: block;
}

.time-labels {
  display: flex;
  justify-content: space-between;
  margin-top: 0.25rem;
  font-size: 0.65rem;
  color: rgba(255, 255, 255, 0.5);
}

.affected-service {
  padding: 0.5rem;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 4px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.service-label {
  font-size: 0.65rem;
  color: rgba(255, 255, 255, 0.5);
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin-bottom: 0.25rem;
}

.service-info {
  display: flex;
  align-items: center;
  gap: 0.4rem;
}

.service-icon {
  font-size: 1rem;
}

.service-name {
  font-family: 'Fira Code', monospace;
  font-size: 0.75rem;
  color: rgba(255, 255, 255, 0.9);
}
</style>
