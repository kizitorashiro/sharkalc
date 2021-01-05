<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <Calc v-bind:title="message" v-on:result-event="appAction" v-bind:mode="mode" v-bind:bgimg="bgimg"/>
    <hr>
    <div>
      <button v-on:click="changeMode('addition')">Addition</button>
      <button v-on:click="changeMode('subtraction')">Subtraction</button>
      <button v-on:click="changeMode('mix')">Mix</button>
  </div>

    <hr>
    <h2> {{ number }}</h2>
    <div v-html="listOfPrizes">   
      <!--
      <img src="./assets/characters/001_megamouse_shark.png" width="50px" />
      <img src="./assets/characters/001_megamouse_shark.png" width="50px" />
      <img src="./assets/characters/001_megamouse_shark.png" width="50px" />
      -->
    </div>
    <div><button v-on:click="clearCalcHistory">Clear History</button></div>
    <hr>
    <div><table v-html="log"></table></div>

  </div>
</template>

<script>
import Calc from './components/Calc.vue'

export default {
  name: 'App',
  components: {
    Calc
  },
  data: () => {
    return {
      message: 'Mental Arithmetric',
      result: [],
      mode: 'addition',
      characters: [],
      bgimg: '',
      prizes: [],
    };
  },
  computed: {
    number: function(){
      return this.result.length;
    },
    log: function() {
      var table = '<tr><th class="head">Expression</th><th class="head">Value</th></tr>';
      for(var i in this.result) {
        table += '<tr><td>' + this.result[i][0]  + '</td><th>' + this.result[i][1] + '</th></tr>'
      }
      return table;
    },
    listOfPrizes: function() {
      var imgs = "";
      console.log("listOfPrizes");
      console.log(this.prizes.length);
      
      for(var i in this.prizes) {
        imgs += `<img src=${this.prizes[i]} width="50px" />`;
      }
      return imgs;
    },
  },
  created: function() {

    var files = require.context('./assets/characters', false);
   //console.log(files.keys());
    files.keys().forEach((key) => {
     //console.log(`${files(key)}`);
     //console.log(`${key}`);
     this.characters.push({
       path: files(key),
       name: key.replace(/^\.\/\d{3}_(.*).png$/,"$1"),
     });
    });

    //var calc_history = JSON.parse(localStorage.getItem('calc_history'));
    //if(calc_history != null) {
    this.loadCalcHistory();
    //}

    if(this.bgimg.length == 0) {
      this.changeImg();
    }
    //var items = localStorage.getItem('log');
    //var logs = JSON.parse(items);
    //if(logs != null) {
    //  this.result = logs;
    //}
  },
  methods: {
    addPrize: function() {
      console.log(this.prizes);
      this.prizes.push(this.bgimg);
    },

    changeImg: function() {
      var index = Math.floor(Math.random() * Math.floor(118));
      while( this.prizes.indexOf(this.characters[index.path]) != -1) {
        index = Math.floor(Math.random() * Math.floor(118));
      }
      this.bgimg = this.characters[index].path;
      this.message = this.characters[index].name;
    },

    changeMode: function(mode) {
      this.mode = mode;
    },

    clearCalcHistory: function() {
      this.result = [];
      this.prizes = [];
      this.bgimg = '';
      this.saveCalcHistory();
      this.changeImg();
    },

    saveCalcHistory: function() {
      var calc_history = {
        result: this.result,
        prizes: this.prizes,
        bgimg: this.bgimg,
      };
      localStorage.setItem('calc_history', JSON.stringify(calc_history));
    },

    loadCalcHistory: function() {
      var calc_history = JSON.parse(localStorage.getItem('calc_history'));
      if(calc_history != null) {
        console.log(`calc ${calc_history}`);
        this.result = calc_history.result;
        this.prizes = calc_history.prizes;
        this.bgimg = calc_history.bgimg;
        console.log(`bgimg =  ${this.bgimg}`);
      }
    },

    appAction: function(result) {
      console.log(result);

      this.result.unshift([result.formula, result.answer]);
      if(this.result.length % 5 == 0) {
        this.addPrize(this.bgimg);
        this.changeImg();
      }

      this.saveCalcHistory();

      //var calc_history = {
      //  result: this.result,
      //  prizes: this.prizes,
      //};
      //localStorage.setItem('calc_history', JSON.stringify(calc_history));
      //if(this.result.length > 10) {
      //  this.result.pop();
      //}
      //var log = JSON.stringify(this.result);
      //localStorage.setItem('log', log);
    },
    //clearHistory: function() {
    //  this.result = [];
    //  this.prizes = [];
    //  
    //}
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
h2 {
  font-size: 32pt;
  font-weight: bold;
  text-align: left;
  color:darkblue;
  margin: 0px;
}
div {
  text-align: left;
}
tr td {
  padding: 5px;
  border: 1px solid gray;
}
tr th  {
  padding: 5px;
  border: 1px solid gray;
}
tr th.head  {
  background-color: black;
  color: white;
}
</style>
