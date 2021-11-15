<template>

 <div class="container-lg" style="height: 1500px;">

            <img src="../assets/img/img1.png" width="100" height="100" class="center_img p-2">
            <h2 class="p-4" style="text-align:center;">Conversor de Moneda</h2>
            <p>De:</p>
            <div class="row">
                <div class="col align-self-center">
                <div class="input-group mb-4">
                <input type="number" class="form-control" v-model="cantidad1" placeholder="Introduzca una cantidad" v-on:keyup="Convertir(de,para)">
                <select class="form-select" id="inputGroupSelect01" v-model="de" v-on:change="Convertir(de,para)">
                <option v-for="(coin,index) in Formato" v-bind:key="index">{{coin}}</option>
                </select>
                </div>
                </div>
                </div>
            <p>Para:</p>
            <div class="row">
                <div class="col align-self-center">
                <div class="input-group mb-4">
                <input type="text" class="form-control" v-model="resultado" disabled>
                <select class="form-select" id="inputGroupSelect02" v-model="para" v-on:change="Convertir(de,para)">
                <option v-for="(coin,index) in Formato" v-bind:key="index">{{coin}}</option>
                </select>
                </div>
                </div>
                </div>
            <div class="row"> 
             <div class="col p-4" style="text-align:center;">
                <h2>Tengo: {{cantidad1}} {{de}} - Equivale: {{resultado}} {{para}}</h2>
             </div>
            </div>
            <div class="container-lg">
            <div class="container align-self-center btn-group btn-group-toggle" data-toggle="buttons">
            <button class="btn btn-lg btn-warning" @click="Guardar()"><b>Guardar Conversión</b></button>
            <button class="btn btn-lg btn-secondary" @click="Vaciar()"><b>Vaciar Historial</b></button>
            </div>
            <h3 style="text-align:center;" class="p-3">Historial de Conversiones:</h3>
            <ul class="list-group p-5">
            <li class="list-group-item list-group-item-dark" v-for="(entrada,index) in entradas" v-bind:key="index" v-on:change="entradas" ><p style="color:rgb(34, 34, 34);">{{entrada}}</p></li>
            </ul>
           
            </div>
            </div>
</template>

<script>

import axios from 'axios';

export default {

    data() {
        return {
        monedas: {},
        valores: {},
        factor : 0,
        de: 'USD',
        para: 'EUR',
        entrada: '',
        entradas: [],
        cantidad1: 0,
        resultado: 0,
        }
    },

    mounted(){

        this.getMonedas(); // Inicio de la Aplicación
     },
            
     computed:{

        Formato(){
          return Object.keys(this.monedas); // Obtiene las Monedas en ISO
        },

    },

    methods: {
 

    async Convertir(moneda1,moneda2){

        // Funcion que permite obtener la hora y la fecha y realiza la conversion de la moneda
        
      var hoy = new Date();  
      var a = moneda1;
      var b = moneda2;
      var fecha = hoy.getDate() + '-' + ( hoy.getMonth() + 1 ) + '-' + hoy.getFullYear();
      var hora = hoy.getHours() + ':' + hoy.getMinutes() + ':' + hoy.getSeconds(); 
      try {
             this.valores = await axios.get('https://free.currconv.com/api/v7/convert?q='+ a + '_' + b +'&compact=ultra&apiKey=13e62537622cb1be3040')
             .then(response => { 
             this.valores = response.data;
             this.factor = parseFloat(Object.values(this.valores));
             this.resultado = this.cantidad1 * this.factor;    
             this.entrada = fecha + ' ' + hora + ' ' + 'De:' + ' ' + this.cantidad1 + ' ' + a + ' ' + 'Para:' + ' ' + b + ' ' + ' ' + 'Total:' + ' ' + this.resultado +  ' ' + b ;
        })
      } catch (e) {

        alert('Fallo en la Consulta del API Free Currence' );

        }
   
        },

    async getMonedas(){

        // Se alimenta de la APi de Fixer para obtener los datos de las monedas en ISO


    const monedas = localStorage.getItem('monedas');

    if(monedas){
    this.monedas = JSON.parse(monedas);
    return;
    }

    try {
    await axios.get('http://data.fixer.io/api/symbols?access_key=5d5c55b6ad20874f32f6f0aa71262389')
    .then(response => {
    this.monedas = response.data.symbols;
    localStorage.setItem('monedas', JSON.stringify(response.data.symbols));
    })
    }

    catch (e) {

        alert('Fallo en la Consulta del API Fixer' );

    }
   
   


},

    Vaciar(){
        if(this.entradas.length === 0 )
        {
            alert("No hay entradas que eliminar")
        }
        else {

        this.entradas = [];

        }   

},

    Guardar(){

        if(this.resultado > 0 ){

             this.entradas.push(this.entrada);

        }

        else{

            alert("No ha realizado ninguna operación, Ingrese una cantidad válida");
        }
    }

    }

}
</script>


<style>
@import url("../assets/css/style.css");
</style>
