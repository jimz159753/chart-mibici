<template>
  <div id="wrapper">
  <canvas id="line" width="800px" height="500px"></canvas>
  <canvas id="doughnut" width="800px" height="500px"></canvas>
  <!-- <h3>{{this.field['top']}}</h3> -->
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
        months: [],
        data_field: {},
        result_genre: {},
        result_age: {},
        result_stations: {},

      };
    },

    methods: {
      add_info: function(){
        
        this.months = [
                      'january',
                      'february',
                      'march',
                      'april',
                      'may',
                      'june',
                      'july',
                      'august',
                      'september',
                      'october',
                      'november',
                      'december',
                      'mibici-stations'
                      ];
        
        this.months.forEach((value) => { 

            fs.readFile("assets/json/"+ value +".json", "utf8",  (err, data)  => {
              if (err) throw err;
              this.data_field[value] = JSON.parse(data);
          
          });
       
        });  

      },

      count_genre: function(){

        var male = {};
        var female = {};


        for(var month in this.data_field){
            
            male[month] = 0;
            female[month] = 0;
          
          for (var i = 0; i < this.data_field[month].length; i++) {

            if (this.data_field[month][i]['Genero'] == 'H') {
              male[month] += 1;

            }
            else {
              female[month] += 1;              
            }

          }

        }

        this.result_genre['count_male'] = Object.values(male);
        this.result_genre['count_female'] = Object.values(female);

      },
      
      years_range: function(){

        this.result_age['18-20'] = 0;
        this.result_age['20-30'] = 0;
        this.result_age['30-more'] = 0;

        for(var month in this.data_field){

          for (var i = 0; i < this.data_field[month].length; i++) {

            if(parseInt(this.data_field[month][i]['A�o_de_nacimiento']) > 1998 && parseInt(this.data_field[month][i]['A�o_de_nacimiento']) <= 2000 ){
            this.result_age['18-20'] += 1;
            }
            else if(parseInt(this.data_field[month][i]['A�o_de_nacimiento']) >= 1988 && parseInt(this.data_field[month][i]['A�o_de_nacimiento']) < 1998 ){
              this.result_age['20-30'] += 1;
            }
            else if(parseInt(this.data_field[month][i]['A�o_de_nacimiento']) < 1988 ){
              this.result_age['30-more'] += 1;
            }

          }

        }

        
       
      },

      station_used : function(){

        this.result_stations['stations'] = [];
        this.data_field['top'] = [];

        for(var month in this.data_field){

          for (var i = 0; i < this.data_field[month].length; i++) {

            this.result_stations['stations'].push(this.data_field[month][i]['Origen_Id']);

          }

        }




        var count = {}
        this.result_stations['stations'].forEach(function(x) { count[x] = (count[x] || 0)+1; });
        this.result_stations = count;


        var arr = Object.keys(this.result_stations).sort().reverse().slice(0,11);

        for (var i = 0; i < this.data_field['mibici-stations'].length; i++) {
          for(var j = 0; j < arr.length; j++){
            if (this.data_field['mibici-stations'][i]['id'] == arr[j]) {
              this.data_field['top'].push(this.data_field['mibici-stations'][i]['name']);
            }
          }
        }
              console.log(this.data_field['top'])
        /*console.log(this.data_field['mibici-stations'])*/
         /* stations more often used importing mibici-stations.json and mapping data to names */
      },

      add_chart: function(){
        this.station_used()
        this.count_genre()
        this.years_range()


        


        const line = document.getElementById("line").getContext("2d");
        
        var ages = new Chart(line, {
        type: 'doughnut',
        data: {
                labels: ['18-20', '20-30', '30-more'] ,
                datasets: [
                {
                    label: 'Ages',
                    data:  [this.result_age['18-20'], this.result_age['20-30'], this.result_age['30-more']],
                    backgroundColor: ["#3e95cd", "#8e5ea2","#3cba9f"],
                    borderColor: [
                        'blue'
                    ],
                    borderWidth: 1
                }
                ]
            },
            options: {
              title: {
                display: true,
                text: 'Ages average'
              },
            }
        });

        const dough = document.getElementById("doughnut").getContext("2d");

        var genres = new Chart(dough, {
        type: 'line',
        data: {
                labels: this.months.slice(0,11) ,
                datasets: [
                {
                    label: 'Male',
                    data:  this.result_genre['count_male'],
                    borderColor: [
                        'blue'
                    ],
                    borderWidth: 1
                },
                {
                label: 'Female',
                    data: this.result_genre['count_female'],
                    borderColor: [
                        'red'
                    ],
                    borderWidth: 1
                }
                ]
            },
            options: {
                title: {
                  display: true,
                  text: 'Genres by month'
                },
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
  
  #genre {
    border: solid;
  }

</style>
