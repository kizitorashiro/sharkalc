<template>
    <div class="base" :style="{ backgroundImage: 'url(' + bgimg + ')'}">
        <h1>{{ title }}</h1>
        <div class="formula_answer">
            <span class="formula">{{ formula }}</span>
            <span class="answer" v-bind:class="answer_status">{{ answer_entered }}</span>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Calc',
    props: {
        title: String,
        mode: String,
        bgimg: String,
    },
    data: function()  {
        return {
            formula: '1 + 1 = ',
            answer_entered: '2',
            answer_expected: '2',
            answer_status: {
                correct: false,
                wrong: false,
            },
        };
    },
    mounted: function() {
        window.addEventListener('keypress', this.keyPressed);
    },
    created: function(){
        console.log(this.formula);  
        this.loadQuestion();

    },
    watch: {
        mode: function(value) {
            console.log(`chage mode to ${value}`);
            this.loadQuestion();
        },
    },
    methods: {
        loadQuestion() {
            var question = this.getQuestion();
            this.formula = question.formula;
            this.answer_expected = question.answer;
            this.answer_entered = '';
            this.answer_status.correct = false;
            this.answer_status.wrong = false;
        },
        clearAnswer() {
            this.answer_entered = '';
            this.answer_status.correct = false;
            this.answer_status.wrong = false;    
        },
        keyPressed(event) {
            console.log('keyPressed');
            if(this.answer_status.correct) {
                if(event.key == 'Enter'){
                    this.loadQuestion();
                }
                return;
            }
            
            if( isNaN(parseInt(event.key, 10))){
                return;
            }

            if(this.answer_status.wrong){
                this.clearAnswer();
            } 

            this.answer_entered = this.answer_entered + event.key;
            if(this.answer_expected.indexOf(this.answer_entered) != 0) {
                this.answer_status.wrong = true;
                this.answer_status.correct = false;
            } else if(this.answer_entered.length == this.answer_expected.length) {
                this.answer_status.correct = true;
                this.answer_status.wrong = false;
                setTimeout(() => {
                    this.$emit('result-event', {
                        formula: this.formula,
                        answer: this.answer_expected,
                        time: new Date(),
                    });
                    this.loadQuestion();
                }, 500);
            } else {
                console.log(this.answer_expected);
                console.log(this.answer_entered);
            }
        },

        getRandomInt(max) {
            return Math.floor(Math.random() * Math.floor(max)) + 1;
        },

        getAdditionOrSubstraction: function() {
            var i = Math.floor(Math.random() * Math.floor(2));
            if(i == 0){
                return this.getAddition();
            } else {
                return this.getSubtraction();
            }
        },

        getAddition: function() {
            var first = this.getRandomInt(9);
            var second = this.getRandomInt(9);

            while(first + second < 10) {
                second = this.getRandomInt(9);
            }

            return {
                formula: `${first} + ${second} = `,
                answer: `${first + second}`,
            }
        },

        getSubtraction: function() {
            var first = this.getRandomInt(19);
            var second = this.getRandomInt(19);

            while(first < second) {
                second = this.getRandomInt(19);
            }

            return {
                formula: `${first} - ${second} = `,
                answer: `${first - second}`,
            }
        },

        getQuestion: function() {
            if(this.mode == 'addition') {
                return this.getAddition();
            } else if(this.mode == 'subtraction'){
                return this.getSubtraction();
            } else if(this.mode == 'mix'){
                return this.getAdditionOrSubstraction();
            }
        },
    }
}
</script>
<style>
.base{
    background-repeat: no-repeat;
}

h1 {
  font-size: 72pt;
  font-weight: bold;
  text-align: right;
  letter-spacing: -8px;
  color:gray;
  margin: 0px;
}

.formula {
  font-size: 100pt;
  font-weight: bold;
  text-align: left;
  letter-spacing: -8px;
  color: black;
  margin: 0px;
}

.answer {
  font-size: 100pt;
  font-weight: bold;
  text-align: left;
  letter-spacing: -8px;
  color: darkgray;
  margin: 0px;
}

.correct {
    color: darkgreen;
}
.wrong {
    color: darkred;
}

.formula_answer {
    text-align: left;
    margin-left: 40px;
}


</style>

