<script setup lang="ts">
import { ref, watch, computed } from 'vue';
import Chart from 'chart.js/auto';
import annotationPlugin from 'chartjs-plugin-annotation';
import { onSlideEnter, onSlideLeave, useSlideContext } from '@slidev/client';

Chart.register(annotationPlugin);

const props = defineProps({
  type: {
    type: String,
    default: 'line', // 'line' or 'bar'
    validator: (value: string) => ['line', 'bar'].includes(value)
  },
  serviceName: {
    type: String,
    default: 'Service'
  },
  metric: {
    type: String,
    default: 'failure-rate' // 'failure-rate' or 'response-time'
  },
  showAnnotation: {
    type: Boolean,
    default: false
  },
  annotationClick: {
    type: Number,
    default: 1 // Which click number triggers the annotation
  },
  spikeClick: {
    type: Number,
    default: 1 // Which click number triggers the spike
  },
  hasIssue: {
    type: Boolean,
    default: true // Whether this service actually has the issue
  },
  height: {
    type: Number,
    default: 300
  },
  width: {
    type: Number,
    default: 600
  },
  maxDataPoints: {
    type: Number,
    default: 60 // Number of data points to show on the chart
  }
});

const chartCanvas = ref<HTMLCanvasElement | null>(null);
let chart: Chart | null = null;
let interval: NodeJS.Timeout | null = null;
let changeAnnotationIndex: number | null = null;

const { $clicks } = useSlideContext();

const metricConfig = computed(() => {
  if (props.metric === 'failure-rate') {
    return {
      label: 'Failure Rate (%)',
      color: 'rgba(239, 68, 68, 1)', // red
      bgColor: 'rgba(239, 68, 68, 0.2)',
      max: 100,
      tickCallback: (value: number) => `${value}%`,
      normalRange: [0, 0.5],
      spikeRange: [25, 45]
    };
  } else {
    return {
      label: 'Response Time (ms)',
      color: 'rgba(59, 130, 246, 1)', // blue
      bgColor: 'rgba(59, 130, 246, 0.2)',
      max: 5000,
      tickCallback: (value: number) => `${value}ms`,
      normalRange: [100, 200],
      spikeRange: [2500, 4000]
    };
  }
});

onSlideEnter(() => {
  // Wait for DOM to be ready before creating chart
  setTimeout(() => {
    const labels = Array.from({ length: props.maxDataPoints }, (_, i) => `T-${props.maxDataPoints - i}s`);
    const data = Array.from({ length: props.maxDataPoints }, () => {
      const [min, max] = metricConfig.value.normalRange;
      return min + Math.random() * (max - min);
    });

    if (chartCanvas.value) {
    chart = new Chart(chartCanvas.value, {
      type: props.type as any,
      data: {
        labels,
        datasets: [
          {
            label: metricConfig.value.label,
            data,
            borderColor: metricConfig.value.color,
            backgroundColor: metricConfig.value.bgColor,
            tension: 0.4,
            fill: true,
            borderWidth: 2
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            display: false
          },
          annotation: {
            annotations: {}
          }
        },
        scales: {
          x: {
            display: false
          },
          y: {
            beginAtZero: true,
            max: metricConfig.value.max,
            ticks: {
              callback: metricConfig.value.tickCallback
            }
          }
        },
        animation: {
          duration: 300
        }
      }
    });

    // Update chart every second with streaming data
    interval = setInterval(() => {
      if (chart) {
        // Remove oldest data point
        chart.data.labels?.shift();
        chart.data.datasets[0].data.shift();

        // Update annotation position as data shifts
        if (changeAnnotationIndex !== null) {
          changeAnnotationIndex--;
          
          if (changeAnnotationIndex < 0) {
            changeAnnotationIndex = null;
            if (chart.options.plugins?.annotation?.annotations) {
              chart.options.plugins.annotation.annotations = {};
            }
          } else {
            if (chart.options.plugins?.annotation?.annotations && 'flagChange' in chart.options.plugins.annotation.annotations) {
              const annotations = chart.options.plugins.annotation.annotations as Record<string, any>;
              annotations.flagChange.xMin = changeAnnotationIndex;
              annotations.flagChange.xMax = changeAnnotationIndex;
            }
          }
        }

        // Add new data point
        const timestamp = new Date().toLocaleTimeString();
        chart.data.labels?.push(timestamp);
        
        // Determine if we should show spike based on clicks and hasIssue
        const shouldSpike = props.hasIssue && $clicks.value >= props.spikeClick;
        const [normalMin, normalMax] = metricConfig.value.normalRange;
        const [spikeMin, spikeMax] = metricConfig.value.spikeRange;
        
        const newValue = shouldSpike
          ? spikeMin + Math.random() * (spikeMax - spikeMin)
          : normalMin + Math.random() * (normalMax - normalMin);

        chart.data.datasets[0].data.push(newValue);
        chart.update('none');
      }
    }, 1000);
  }

  // Watch for clicks to add annotation
  watch(() => $clicks.value, (newVal) => {
    if (chart && props.showAnnotation && newVal >= props.annotationClick) {
      if (changeAnnotationIndex === null) {
        // Add annotation when the specified click happens
        const currentDataIndex = chart.data.labels?.length ? chart.data.labels.length - 1 : 0;
        changeAnnotationIndex = currentDataIndex;
        
        if (chart.options.plugins?.annotation?.annotations) {
          chart.options.plugins.annotation.annotations = {
            flagChange: {
              type: 'line',
              xMin: currentDataIndex,
              xMax: currentDataIndex,
              borderColor: 'rgb(168, 85, 247)', // purple
              borderWidth: 3,
              borderDash: [6, 6],
              label: {
                display: true,
                content: '🚩 Flag Changed',
                position: 'start',
                yAdjust: -120,
                backgroundColor: 'rgba(168, 85, 247, 0.9)',
                color: 'white',
                font: {
                  size: 11,
                  weight: 'bold'
                },
                padding: 4
              }
            }
          };
          chart.update('none');
        }
      }
    }
  });
  }, 100);
});

onSlideLeave(() => {
  if (interval) {
    clearInterval(interval);
  }
  if (chart) {
    chart.destroy();
  }
});
</script>

<template>
  <div>
    <div class="text-sm font-semibold mb-2 opacity-80">{{ serviceName }}</div>
    <div :style="{ width: `${width}px`, height: `${height}px` }">
      <canvas ref="chartCanvas"></canvas>
    </div>
  </div>
</template>
