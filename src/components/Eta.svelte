<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto';

  let canvasElement;

  // Dati statici per il grafico
  const ageData = {
    labels: ['18-24', '25-34', '35-44', '45-54', '55-64', '65+'],
    data: [250, 300, 100, 50, 75, 30] // Numero di compratori per ogni fascia di età
  };

  // Viene chiamato non appena il componente viene montato
  onMount(() => {
    updateBarChart(ageData);
  });

  function updateBarChart(ageData) {
    const data = {
      labels: ageData.labels,
      datasets: [{
        label: 'Numero di Compratori per età',
        data: ageData.data,
        backgroundColor: 'rgba(54, 162, 235, 0.5)',
        borderColor: 'rgba(54, 162, 235, 1)',
        borderWidth: 1,
      }]
    };

    const config = {
      type: 'bar',
      data: data,
      options: {
        responsive: true,
        maintainAspectRatio: false,
        animation: {
          duration: 800, 
        },
        scales: {
          x: {
            title: {
              display: true,
              text: 'Età'
            }
          },
          y: {
            title: {
              display: true,
              text: 'Numero di Compratori'
            }
          }
        }
      }
    };
    new Chart(canvasElement, config);
  }
</script>

<style> 
div { 
  margin-top: 20px; 
} 
canvas { 
  max-width: 100%; 
  height: 500px; 
  margin: auto;
}
</style>

<div>
  <canvas bind:this={canvasElement}></canvas>
</div>
