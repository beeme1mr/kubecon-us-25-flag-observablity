<template>
  <div class="timeline">
    <div 
      v-for="(event, index) in events" 
      :key="index"
      class="timeline-item"
      :class="{ 'visible': visibleItems > index }"
    >
      <div class="timeline-marker" :class="event.severity" />
      <div class="timeline-content">
        <div class="flex items-center gap-2">
          <span class="timeline-label" :class="event.severity">{{ event.label }}</span>
          <span class="timeline-time">{{ event.time }}</span>
        </div>
        <div class="timeline-text">{{ event.text }}</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">

interface TimelineEvent {
  time: string
  label: string
  text: string
  severity: 'error' | 'warning' | 'info'
}

const props = defineProps({
  visibleItems: {
    type: Number,
    default: 0
  }
})

const events: TimelineEvent[] = [
  {
    time: '3:15 PM',
    label: 'ERROR',
    text: 'Failure rate increase detected',
    severity: 'error'
  },
  {
    time: '3:20 PM',
    label: 'INVESTIGATION',
    text: 'No recent deployments found',
    severity: 'warning'
  },
  {
    time: '3:55 PM',
    label: 'INVESTIGATION',
    text: 'No code changes in last 48h',
    severity: 'warning'
  },
  {
    time: '4:40 PM',
    label: 'INVESTIGATION',
    text: 'All dependencies healthy',
    severity: 'warning'
  },
  {
    time: '5:10 PM',
    label: 'ESCALATION',
    text: 'All hands on deck',
    severity: 'error'
  },
  {
    time: '6:15 PM',
    label: 'ROOT CAUSE',
    text: '"Oh, I toggled that feature flag"',
    severity: 'info'
  }
]
</script>

<style scoped>
.timeline {
  position: relative;
  padding-left: 1.5rem;
}

.timeline-item {
  position: relative;
  padding-bottom: 1rem;
  opacity: 0;
  transform: translateX(-10px);
  transition: all 0.4s ease-out;
}

.timeline-item.visible {
  opacity: 1;
  transform: translateX(0);
}

.timeline-item::before {
  content: '';
  position: absolute;
  left: -1.05rem;
  top: 0.4rem;
  bottom: -.2rem;
  width: 2px;
  background: rgba(255, 255, 255, 0.2);
}

.timeline-item:last-child::before {
  display: none;
}

.timeline-marker {
  position: absolute;
  left: -1.3rem;
  top: 0.2rem;
  width: 0.6rem;
  height: 0.6rem;
  border-radius: 50%;
  border: 2px solid currentColor;
  background: #1a1a2e;
}

.timeline-marker.error {
  color: #f87171;
  position: relative;
}

.timeline-marker.warning {
  color: #fb923c;
}

.timeline-marker.info {
  color: #60a5fa;
}

.timeline-content {
  padding-left: 0.5rem;
}

.timeline-time {
  font-size: 0.65rem;
  color: rgba(255, 255, 255, 0.5);
  font-family: 'Fira Code', monospace;
  margin-bottom: 0.15rem;
}

.timeline-label {
  font-size: 0.65rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin-bottom: 0.15rem;
}

.timeline-label.error {
  color: #f87171;
}

.timeline-label.warning {
  color: #fb923c;
}

.timeline-label.info {
  color: #60a5fa;
}

.timeline-text {
  font-size: 0.8rem;
  color: rgba(255, 255, 255, 0.85);
  line-height: 1.3;
}
</style>
