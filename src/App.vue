<template>
<div>
  <div>
    <div v-for="(element,index) in questions.slice(a,b)" :key="index" v-show="running">
      <div>
        <div id="clock" class="text-principal">
          TIEMPO {{time}}
        </div>
        <div class="d-flex align-items-center bg-secundario py-3">
          <h2 class="ml-auto mr-3 font-weight-bold text-principal">
            {{b}}
          </h2>
          <div class="ml-3 mr-auto text-center w-75 text-negro">
            {{element.question}}
          </div>
        </div>
      </div>
      <div class="text-center">
          <button v-for="(item,index) in element.suggestions"
              :key="index"
              class="btn btn-lg text-light mr-2 px-5 mt-3"
              :class="select ? check(item) : 'bg-secondary'"
              @click="selectResponse(item)"
              >{{item.suggestion}}</button>
      </div>
    </div>
    <div class="box-score" v-if="score_show">
      <h2>¡GRACIAS POR RESPONDER! </h2>
      <p class="text-negro bg-secundario p-4">RESPONDIÓ CORRECTAMENTE <span class="text-principal font-weight-bold">{{score}}</span> PREGUNTAS EN <span class="text-principal font-weight-bold">{{time}}</span> SEGUNDOS</p>
      <p class="font-italic">Para participar, por favor complete estos datos:</p>
      <div class="row">
        <div class="col-md-6">
          <div class="form-group floating-label-form-group controls">
            <input type="text" class="form-control" placeholder="Nombre" v-model="name" id="name" name="name" required="required">
            <p class="help-block text-danger">  </p>
          </div>
          <div class="form-group floating-label-form-group controls">
            <input type="text" class="form-control" placeholder="DNI" v-model="dni" id="dni" name="dni" required="required">
            <p class="help-block text-danger">  </p>
          </div>
        </div>
        <div class="col-md-6">
          <div class="form-group floating-label-form-group controls">
            <input type="email" class="form-control" placeholder="Email" v-model="email" id="email" name="email" required="required">
            <p class="help-block text-danger">  </p>
          </div>
          <div class="form-group floating-label-form-group controls">
            <input type="text" class="form-control" placeholder="Teléfono" v-model="telefono" id="telefono" name="telefono" required="required">
            <p class="help-block text-danger">  </p>
          </div>
        </div>
      </div>
      <div class="form-group">
        <button @click="guardaDatos"
                class="btn btn-lg px-5 btn-outline-danger font-weight-bold"
                id="sendMessageButton"
                style="border: 3px solid"
                >ENVIAR</button>
      </div>
      <p class="font-italic text-negro">Nos comunicaremos para avisarle en caso de haber ganado un premio.</p>
      <h3>{{resultadoJ}}</h3>
    </div>
    <div class="quiz-footer" v-show="quiz">
      <div class="box-button" v-show="playing">
        <h3 class="font-weight-bold">RESPONDA LA TRIVIA, COMPLETE SUS DATOS Y PARTICIPE</h3>
        <p class="bg-secundario p-3"><span>PREMIOS: <span class="text-principal">10</span> LOREM IPSUM DOLOR CONSECTETUER - <span class="text-principal">100</span> LOREM IPSUM DOLOR </span><br>
        <span class="font-italic">para quienes respondan más preguntas correctas en menos tiempo</span></p>
        <button @click="start"
                type="button"
                class="btn btn-lg btn-outline-danger px-5"
                style="border: 3px solid"
                > EMPEZAR </button>
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
      return 'bg-success'
    }else{
      return 'bg-danger'
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
  , min = timeElapsed.getUTCMinutes()
  , sec = timeElapsed.getUTCSeconds();

  this.time =
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
