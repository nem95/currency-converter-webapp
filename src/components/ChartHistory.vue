<template>
  <div class="table-auto w-full lg:max-w-full  mt-12 bg-gray-100">
    <canvas id="myChart" width="400" height="400"></canvas>
  </div>
</template>

<script>
import Chart from 'chart.js';

export default {
  name: 'ChartHistory',
  props: {
    history: Object,
  },
  watch: {
    history: function (newVal) {
      this.history = newVal;
      this.setChart();
    }
  },
  mounted: function () {
    this.setChart();
  },
  methods: {
    setChart: function () {
      var ctx = document.getElementById('myChart').getContext('2d');
      new Chart(ctx, {
        type: 'line',
        data: {
          labels: Object.keys(this.history),
          datasets: [{
            label: '# of Votes',
            data: Object.values(this.history),
            backgroundColor: [
              'rgba(255, 99, 32, 0.2)',
              'rgba(54, 162, 235, 0.2)',
              'rgba(255, 206, 86, 0.2)',
              'rgba(75, 192, 192, 0.2)',
              'rgba(153, 102, 255, 0.2)',
              'rgba(255, 159, 64, 0.2)'
            ],
            borderColor: [
              'rgba(255, 99, 132, 1)',
              'rgba(54, 162, 235, 1)',
              'rgba(255, 206, 86, 1)',
              'rgba(75, 192, 192, 1)',
              'rgba(153, 102, 255, 1)',
              'rgba(255, 159, 64, 1)'
            ],
            borderWidth: 1,
            fill: false,
          }]
        },
        options: {
          scales: {
            yAxes: [{
              ticks: {

              }
            }]
          }
        }
      });
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
