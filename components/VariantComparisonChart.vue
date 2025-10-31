<script setup lang="ts">
import { ref, watch, computed } from 'vue';
import Chart from 'chart.js/auto';
import annotationPlugin from 'chartjs-plugin-annotation';
import { onSlideEnter, onSlideLeave, useSlideContext } from '@slidev/client';

Chart.register(annotationPlugin);

const chartCanvas = ref<HTMLCanvasElement | null>(null);
let chart: Chart | null = null;

const { $clicks } = useSlideContext();

// Compute which view mode based on clicks (offset by 1 since chart appears at click 5)
const currentView = computed(() => {
  const relativeClicks = $clicks.value - 4; // Chart appears at click 5
  if (relativeClicks >= 3) return 2; // Split by variant (clicks 8+)
  if (relativeClicks >= 2) return 1; // Flag traffic only (clicks 7)
  if (relativeClicks >= 1) return 0; // All traffic (clicks 6)
  return -1; // Not yet visible
});

// Generate hardcoded data for consistent demo
function generateData() {
  const dataPoints = 60;
  const flagTogglePoint = 30; // Flag toggles halfway through
  
  // Baseline healthy response time with minimal variance
  const baseline = () => 165 + Math.random() * 20; // 165-185ms
  
  // View 1: All traffic (barely noticeable change)
  // 80% of requests stay healthy, 20% become slow
  const allTraffic = Array.from({ length: dataPoints }, (_, i) => {
    if (i < flagTogglePoint) return baseline();
    // After flag: slight bump (most traffic unaffected)
    return baseline() + (Math.random() * 40); // 165-225ms (barely noticeable)
  });
  
  // View 2: Flag traffic only (clear degradation)
  // Shows only the 40% of traffic that uses the flag
  // 50% get control (healthy), 50% get on (slow) = clear spike
  const flagTraffic = Array.from({ length: dataPoints }, (_, i) => {
    if (i < flagTogglePoint) return baseline();
    // After flag: clear increase (average of control + on)
    return 1600 + Math.random() * 400; // 1600-2000ms (clear spike)
  });
  
  // View 3: Split by variant
  // Control: always healthy
  const controlVariant = Array.from({ length: dataPoints }, () => baseline());
  
  // On: problematic after flag
  const onVariant = Array.from({ length: dataPoints }, (_, i) => {
    if (i < flagTogglePoint) return baseline();
    return 3200 + Math.random() * 600; // 3200-3800ms (obvious problem)
  });
  
  return {
    labels: Array.from({ length: dataPoints }, (_, i) => `${i}s`),
    flagTogglePoint,
    allTraffic,
    flagTraffic,
    controlVariant,
    onVariant
  };
}

onSlideEnter(() => {
  const data = generateData();

  if (chartCanvas.value) {
    chart = new Chart(chartCanvas.value, {
      type: 'line',
      data: {
        labels: data.labels,
        datasets: [
          {
            label: 'All Traffic',
            data: data.allTraffic,
            borderColor: 'rgba(156, 163, 175, 1)',
            backgroundColor: 'rgba(156, 163, 175, 0.1)',
            tension: 0.4,
            fill: true,
            borderWidth: 2,
            hidden: false
          },
          {
            label: 'With Flag',
            data: data.flagTraffic,
            borderColor: 'rgba(168, 85, 247, 1)',
            backgroundColor: 'rgba(168, 85, 247, 0.1)',
            tension: 0.4,
            fill: true,
            borderWidth: 2,
            hidden: true
          },
          {
            label: 'Variant: control',
            data: data.controlVariant,
            borderColor: 'rgba(34, 197, 94, 1)',
            backgroundColor: 'rgba(34, 197, 94, 0.1)',
            tension: 0.4,
            fill: true,
            borderWidth: 2,
            hidden: true
          },
          {
            label: 'Variant: on',
            data: data.onVariant,
            borderColor: 'rgba(239, 68, 68, 1)',
            backgroundColor: 'rgba(239, 68, 68, 0.1)',
            tension: 0.4,
            fill: true,
            borderWidth: 2,
            hidden: true
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            position: 'bottom',
            labels: {
              filter: (item) => {
                const index = item.datasetIndex ?? 0;
                if (currentView.value === 0) return index === 0;
                if (currentView.value === 1) return index === 1;
                return index >= 2;
              }
            }
          },
          annotation: {
            annotations: {
              flagChange: {
                type: 'line',
                xMin: data.flagTogglePoint,
                xMax: data.flagTogglePoint,
                borderColor: 'rgb(168, 85, 247)',
                borderWidth: 3,
                borderDash: [6, 6],
                label: {
                  display: true,
                  content: '🚩 Flag Toggled',
                  position: 'start',
                  yAdjust: -130,
                  backgroundColor: 'rgba(168, 85, 247, 0.9)',
                  color: 'white',
                  font: {
                    size: 11,
                    weight: 'bold'
                  },
                  padding: 4
                }
              }
            }
          }
        },
        scales: {
          x: {
            display: false
          },
          y: {
            beginAtZero: true,
            max: 300,
            ticks: {
              callback: (value) => `${value}ms`
            }
          }
        },
        animation: {
          duration: 500,
          easing: 'easeInOutQuad'
        }
      }
    });

    // Update visibility based on current view
    updateVisibility();
  }

  // Watch for view changes
  watch(currentView, () => {
    updateVisibility();
  });
});

function updateVisibility() {
  if (!chart || !chart.options.scales?.y) return;
  
  if (currentView.value === 0) {
    // Show only "All Traffic" - max 300ms for subtle changes
    chart.data.datasets[0].hidden = false;
    chart.data.datasets[1].hidden = true;
    chart.data.datasets[2].hidden = true;
    chart.data.datasets[3].hidden = true;
    chart.options.scales.y.max = 300;
  } else if (currentView.value === 1) {
    // Show only "With Flag" - max 2500ms for clear spike
    chart.data.datasets[0].hidden = true;
    chart.data.datasets[1].hidden = false;
    chart.data.datasets[2].hidden = true;
    chart.data.datasets[3].hidden = true;
    chart.options.scales.y.max = 2500;
  } else if (currentView.value === 2) {
    // Show variant split - max 4500ms for dramatic difference
    chart.data.datasets[0].hidden = true;
    chart.data.datasets[1].hidden = true;
    chart.data.datasets[2].hidden = false;
    chart.data.datasets[3].hidden = false;
    chart.options.scales.y.max = 4500;
  } else {
    // Not yet visible
    chart.data.datasets.forEach(ds => ds.hidden = true);
  }
  
  // Use default animation (not 'none') to smoothly transition Y-axis and data
  chart.update();
}

onSlideLeave(() => {
  if (chart) {
    chart.destroy();
  }
});
</script>

<template>
  <div>
    <div class="text-sm font-semibold mb-2 opacity-80">Response Time</div>
    <div style="width: 400px; height: 200px;">
      <canvas ref="chartCanvas"></canvas>
    </div>
  </div>
</template>
