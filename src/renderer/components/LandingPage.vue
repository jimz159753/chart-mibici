<template>
  <div id="wrapper">
  <canvas id="testing" width="800px" height="500px"></canvas>
    <button @click="add_chart">button</button>
    
  </div>
</template>

<script>

  import { Bar } from 'vue-chartjs'
  import fs from "fs";

  export default {

    name: 'landing-page',

    data() {
      return{

        data_field: [],
        result: {},

      };
    },

    methods: {
      
      add_info: function(){
        
        fs.readFile("assets/test.json", "utf8",  (err, data)  => {
            if (err) throw err;
            this.data_field = JSON.parse(data);
        });
        
      },

      count_genre: function(){

        this.result['count_male'] = 0;
        this.result['count_female'] = 0;

        for(var i=0; i<this.data_field.length; i++){

          if(this.data_field[i]['Genero'] == 'M' ){
            this.result['count_male'] += 1;
          }
          else{
            this.result['count_female'] += 1;
          }

        }
      },
      
      years_range: function(){

        this.result['18-20'] = 0;
        this.result['20-30'] = 0;
        this.result['30-more'] = 0;

        for(var i=0; i<this.data_field.length; i++){

          if(parseInt(this.data_field[i]['A�o_de_nacimiento']) > 1998 && parseInt(this.data_field[i]['A�o_de_nacimiento']) <= 2000 ){
            this.result['18-20'] += 1;
          }
          else if(parseInt(this.data_field[i]['A�o_de_nacimiento']) >= 1988 && parseInt(this.data_field[i]['A�o_de_nacimiento']) < 1998 ){
            this.result['20-30'] += 1;
          }
          else if(parseInt(this.data_field[i]['A�o_de_nacimiento']) < 1988 ){
            this.result['30-more'] += 1;
          }

        }
      },

      add_chart: function(){

        this.count_genre()
        this.years_range()


        const ctx = document.getElementById("testing").getContext("2d");
        
        var myChart = new Chart(ctx, {
        type: 'line',
        data: {
                labels: Object.keys(this.result),
                datasets: [{
                    label: '# of Votes',
                    data: Object.values(this.result),
                    backgroundColor: [
                        'rgba(255, 99, 132, 0.2)'
                        
                    ],
                    borderColor: [
                        'rgba(255,99,132,1)'
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
    },
    
    mounted() {
    this.add_info();
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
