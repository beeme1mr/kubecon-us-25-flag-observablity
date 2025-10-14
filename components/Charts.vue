<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue';
import Chart from 'chart.js/auto';
import annotationPlugin from 'chartjs-plugin-annotation';
import { useSlideContext } from '@slidev/client';

Chart.register(annotationPlugin);

const failureChart = ref<HTMLCanvasElement | null>(null);
let chart: Chart | null = null;
let interval: NodeJS.Timeout | null = null;
let changeAnnotationIndex: number | null = null;

const { $clicks, $nav } = useSlideContext();

const resetChartData = () => {
  if (chart) {
    const maxDataPoints = 60;
    chart.data.labels = Array.from({ length: maxDataPoints }, (_, i) => `T-${maxDataPoints - i}s`);
    chart.data.datasets[0].data = Array.from({ length: maxDataPoints }, () => 0);
    // chart.data.datasets[0].borderColor = 'rgb(75, 192, 192)';
    // chart.data.datasets[0].backgroundColor = 'rgba(75, 192, 192, 0.2)';
    
    // Remove annotation
    if (chart.options.plugins?.annotation?.annotations) {
      chart.options.plugins.annotation.annotations = {};
    }
    changeAnnotationIndex = null;
    
    chart.update('none');
  }
};

onMounted(() => {
  const maxDataPoints = 60;
  const labels = Array.from({ length: maxDataPoints }, (_, i) => `T-${maxDataPoints - i}s`);
  const data = Array.from({ length: maxDataPoints }, () => 0);

  if (failureChart.value) {
    chart = new Chart(failureChart.value, {
      type: 'bar',
      data: {
        labels,
        datasets: [
          {
            label: 'Failure Rate (%)',
            data,
            borderColor: 'rgba(192, 75, 75, 1)',
            backgroundColor: 'rgba(255, 49, 62, 0.2)',
            tension: 0.4,
            fill: true
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            position: 'bottom'
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
            max: 100,
            ticks: {
              callback: (value) => `${value}%`
            }
          }
        },
        animation: {
          duration: 300
        }
      }
    });

    // Update chart every second
    interval = setInterval(() => {
      if (chart) {
        // Remove oldest data point
        chart.data.labels?.shift();
        chart.data.datasets[0].data.shift();

        // Decrement annotation index as data shifts left
        if (changeAnnotationIndex !== null) {
          changeAnnotationIndex--;
          
          // Remove annotation if it scrolls off the left
          if (changeAnnotationIndex < 0) {
            changeAnnotationIndex = null;
            if (chart.options.plugins?.annotation?.annotations) {
              chart.options.plugins.annotation.annotations = {};
            }
          } else {
            // Update annotation position
            if (chart.options.plugins?.annotation?.annotations?.flagChange) {
              chart.options.plugins.annotation.annotations.flagChange.xMin = changeAnnotationIndex;
              chart.options.plugins.annotation.annotations.flagChange.xMax = changeAnnotationIndex;
            }
          }
        }

        // Add new data point
        const timestamp = new Date().toLocaleTimeString();
        chart.data.labels?.push(timestamp);
        
        // Spike when clicked ($clicks > 0), otherwise keep low
        const newValue = $clicks.value > 0 
          ? 25 + Math.random() * 20  // High failure rate: 25-45%
          : Math.random() * 0.5;      // Low baseline: 0-0.5%

        chart.data.datasets[0].data.push(newValue);
        chart.update('none');
      }
    }, 1_000);
  }

  // Watch for clicks to add annotation
  watch(() => $clicks.value, (newVal, oldVal) => {
    if (chart && newVal > 0 && oldVal === 0) {
      // Add annotation when first click happens
      const currentDataIndex = chart.data.labels?.length ? chart.data.labels.length - 1 : 0;
      changeAnnotationIndex = currentDataIndex;
      
      if (chart.options.plugins?.annotation?.annotations) {
        chart.options.plugins.annotation.annotations = {
          flagChange: {
            type: 'line',
            xMin: currentDataIndex,
            xMax: currentDataIndex,
            borderColor: 'rgb(255, 159, 64)',
            borderWidth: 3,
            borderDash: [6, 6],
            label: {
              display: true,
              content: '🚩 Feature Flag Changed',
              position: 'end',
              yAdjust: 10,
              backgroundColor: 'rgba(255, 159, 64, 0.8)',
              color: 'white',
              font: {
                size: 12,
                weight: 'bold'
              },
              padding: 6
            }
          }
        };
        chart.update('none');
      }
    }
  });

  // Watch for slide changes and reset the chart
  watch(() => $nav.currentPage, () => {
    resetChartData();
  });
});

onUnmounted(() => {
  if (interval) {
    clearInterval(interval);
  }
  if (chart) {
    chart.destroy();
  }
});
</script>

<template>
  <div style="width: 800px; height: 400px;">
    <canvas ref="failureChart"></canvas>
  </div>
</template>