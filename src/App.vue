<template>
<div class="container-app">
    <div class="container-quiz">
        <div class="quiz-header">
          <h1>TRIVIA QUAZEMIC APSA</h1>
        </div>
        <div class="quiz-main" v-for="(element,index) in questions.slice(a,b)" :key="index" v-show="running">
            <div class="box-question">
              <h2>Question {{b}}/{{questions.length}}</h2>
              <div id="clock">
              <p class="text">Tiempo: {{time}}</p>
              </div>
              <p>{{element.question}}</p>
            </div>
            <div class="box-suggestions">
              <ul>
              <li v-for="(item,index) in element.suggestions" :key="index" :class="select ? check(item) : ''" @click="selectResponse(item)">{{item.suggestion}}</li>
              </ul> 
            </div>
        </div>
        <div class="box-score" v-if="score_show">
          <h2>¡Gracias por responder! </h2>
          <h3>Tu puntuación es: <strong style="color:red">{{score}}/6</strong></h3>        
          <h3>Tiempo utilizado: <strong style="color:red">{{time}}</strong></h3>
        <div class="section">
         <hr />
        </div>
        <p>Para participar, por favor, completá los siguientes campos:</p>
        <div class="form-group floating-label-form-group controls">

              <input type="text" class="form-control" value="" placeholder="Nombre" v-model="name" id="name" name="name">
               <p class="help-block text-danger">  </p>             
        </div>
         <div class="form-group floating-label-form-group controls">
               <input type="text" class="form-control" value="" placeholder="DNI" v-model="dni" id="dni" name="dni">
               <p class="help-block text-danger">  </p>             
          </div>
      
              <div class="form-group floating-label-form-group controls">

              <input type="text" class="form-control" value="" placeholder="Email" v-model="email" id="email" name="email">
               <p class="help-block text-danger">  </p>             
               </div>
               
              <div class="form-group floating-label-form-group controls">
         
              <input type="text" class="form-control" value="" placeholder="Teléfono" v-model="telefono" id="telefono" name="telefono">
               <p class="help-block text-danger">  </p>             
               </div>
               <div class="form-group">
            <button @click="guardaDatos" class="btn btn-primary" id="sendMessageButton">Participar</button> </div>
             <h3>{{resultadoJ}}</h3>
        </div>
        <div class="quiz-footer" v-show="quiz">
          
         <div class="box-button" v-show="playing">
         <button @click="start">Empezar</button>
          </div>
        </div>
    </div>
</div>
</template>

<script>
import axios from 'axios';
export default {

  name: 'App',
  data(){
    return {questions:[
      {
        question:'CLONAZEPAM, principio activo de QUAZEMIC,  ¿es la benzodiazepina de mayor potencia relativa?',
        suggestions:[
          {suggestion:'Sí',correct:true},
          {suggestion:'No'},
        ]
      },
      {
        question:'La vida media de QUAZEMIC es:',
        suggestions:[
          {suggestion:'4 horas'},
          {suggestion:'1 hora'},
          {suggestion:'Mayor ', correct:true},
        ]
      },
      {
        question:'¿QUAZEMIC está  indicado en Trastornos de angustia con o sin agorafobia?',
        suggestions:[
          {suggestion:'Sí',correct:true},
          {suggestion:'No'},
        ]
      },
      {
        question:'¿QUAZEMIC es apto para pacientes celíacos?',
        suggestions:[
          {suggestion:'Sí',correct:true},
          {suggestion:'No'},
        ]
      },
      {
        question:'La dosis inicial para adultos con trastorno de pánico es 0,5 mg /día.',
        suggestions:[
          {suggestion:'Sí',correct:true},
          {suggestion:'No'},
        ]
      },
      {
        question:'QUAZEMIC 0,5mg y 2mg  se presentan en envases de:',
        suggestions:[
          {suggestion:'30 y 60 comprimidos',correct:true},
          {suggestion:'Sólo 30 comprimidos'},
          {suggestion:'Sólo 28 comprimidos'},
        ]
      },

     ],

     a:0,
     b:1,
     select:false,
     score:0,
     quiz:true,
     score_show:false,
     time: "",
     started:null,
     running:false,
     timeBegan : null,
     timeStopped : null,
     stoppedDuration : 0,
     playing:true,
     dni:"",
     nombre:"",
     email:"",
     telefono:"",
     resultadoJ:"",

     
     

    }
  },
methods:{

  guardaDatos: async function(){
  var info = {
    nombre:this.name,
    email:this.email,
    dni:this.dni,
    telefono:this.telefono,
    puntaje:this.score,
    tiempo:this.time,
  };

  await axios.post('/guardaDatos.php',info)
    .then((response) => {
          this.resultadoJ = response.data
        })
    .catch(function(error){
      console.log(error);
    });


},


randomize() {
      for (let i = this.questions.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    const temp = this.questions[i];
    this.questions[i]= this.questions[j]; 
    this.questions[j]= temp;
  }
    },

  selectResponse(e) {
    this.select = true;

    if(e.correct){
      this.score++;
    }

      setTimeout(() => {
      this.nextQuestion();
    }, 1000);
  },

  check(status){
    if(status.correct){    
      return 'correct'
    }else{
      return 'incorrect'
    }

    },

  nextQuestion(){

    if(this.questions.length -1 == this.a){
      this.stop();
      this.score_show=true;
      this.quiz=false;
    }else{
      this.a++;
      this.b++;
      this.select=false;
    }
    },

    skipQuestion(){

    if(this.questions.length -1 == this.a){
      this.stop()
      this.score_show=true;
      this.quiz=false;
    }else{
      this.a++;
      this.b++;
      this.select=false;
    }


  },

  clockRunning(){
  var currentTime = new Date()
  , timeElapsed = new Date(currentTime - this.timeBegan - this.stoppedDuration)
  , hour = timeElapsed.getUTCHours()
  , min = timeElapsed.getUTCMinutes()
  , sec = timeElapsed.getUTCSeconds();

  this.time = 
    this.zeroPrefix(hour, 2) + ":" + 
    this.zeroPrefix(min, 2) + ":" + 
    this.zeroPrefix(sec, 2);
},

 zeroPrefix(num, digit) {
  var zero = '';
  for(var i = 0; i < digit; i++) {
    zero += '0';
  }
  return (zero + num).slice(-digit);
},

 start() {
  this.randomize();
  this.playing=false;
  if(this.running) return;
  
  if (this.timeBegan === null) {
    
    this.timeBegan = new Date();
  }

  if (this.timeStopped !== null) {
    this.stoppedDuration += (new Date() - this.timeStopped);
  }

  this.started = setInterval(this.clockRunning, 10);  
  this.running = true;
},

stop() {
  this.running = false;
  this.timeStopped = new Date();
  clearInterval(this.started);
  }
}



}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
