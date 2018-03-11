<template>
  <div id="wrapper">
  <canvas id="testing" width="800px" height="500px"></canvas>
    <button @click="abc">button</button>
    <p>{{nombre}} {{apellido}}</p>
  </div>
</template>

<script>
  import { Bar } from 'vue-chartjs'
  import fs from "fs";

  export default {
    name: 'landing-page',
    data() {
      return{
        nombre: "luis",
        apellido: "jimenez",
      };
    },
    methods: {
      abc: function(){
        fs.readFile("assets/mibici-viajes.json", "utf8",  (err, data)  => {
            if (err) throw err;
            const obj = JSON.parse(data);
            console.log(this.nombre)
        });
      }
    },
    mounted() {
      const ctx = document.getElementById("testing").getContext("2d");
      var myChart = new Chart(ctx, {
    type: 'bar',
    data: {
            labels: ["Red", "Blue", "Yellow", "Green", "Purple", "Orange"],
            datasets: [{
                label: '# of Votes',
                data: [12, 19, 3, 5, 2, 3],
                backgroundColor: [
                    'rgba(255, 99, 132, 0.2)',
                    'rgba(54, 162, 235, 0.2)',
                    'rgba(255, 206, 86, 0.2)',
                    'rgba(75, 192, 192, 0.2)',
                    'rgba(153, 102, 255, 0.2)',
                    'rgba(255, 159, 64, 0.2)'
                ],
                borderColor: [
                    'rgba(255,99,132,1)',
                    'rgba(54, 162, 235, 1)',
                    'rgba(255, 206, 86, 1)',
                    'rgba(75, 192, 192, 1)',
                    'rgba(153, 102, 255, 1)',
                    'rgba(255, 159, 64, 1)'
                ],
                borderWidth: 1
            }]
        },
        options: {
            scales: {
                yAxes: [{
                    ticks: {
                        beginAtZero:true
                    }
                }]
            }
        }
    });


    }
  }
</script>

<style>
  #wrapper{
    width: 500px;
    height: 500px;
  }
  #myChart {
    border: solid;
  }

</style>
