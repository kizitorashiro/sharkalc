<template>
  <div id="app">
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
    </div>
    <!--
    <div><button v-on:click="clearCalcHistory">Clear History</button></div>
    -->
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
      for(var i in this.prizes) {
        imgs += `<img src=${this.prizes[i]} width="50px" />`;
      }
      return imgs;
    },
  },
  created: function() {

    var files = require.context('./assets/characters', false);
    files.keys().forEach((key) => {
     this.characters.push({
       path: files(key),
       name: key.replace(/^\.\/\d{3}_(.*).png$/,"$1"),
     });
    });

    this.loadCalcHistory();

    if(this.bgimg.length == 0) {
      this.changeImg();
    }
  },
  methods: {
    addPrize: function() {
      this.prizes.push(this.bgimg);
    },

    changeImg: function() {
      var index = Math.floor(Math.random() * Math.floor(this.characters.length));
      if(this.prizes.length < this.characters.length) {
        while( this.prizes.indexOf(this.characters[index].path) != -1) {
          index = Math.floor(Math.random() * Math.floor(this.characters.length));
        }
        this.bgimg = this.characters[index].path;
        this.message = this.characters[index].name;
      } else {
        this.bgimg = this.characters[index].path;
        this.message = 'congratulations! You have completed.';
      } 
      
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
        this.result = calc_history.result;
        this.prizes = calc_history.prizes;
        this.bgimg = calc_history.bgimg;
      }
    },

    appAction: function(result) {
      this.result.unshift([result.formula, result.answer]);
      if(this.result.length % 5 == 0) {
        this.addPrize(this.bgimg);
        this.changeImg();
      }
      this.saveCalcHistory();
    },
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
