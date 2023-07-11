<template>
  <div>

    <ScoreBoard v-bind:winCount="this.winCount" v-bind:loseCount="this.loseCount"/>

    <template v-if="this.question">
      <h1 v-html="this.question"></h1>
  
      <template v-for="(answer, index) in this.answers" v-bind:key="index">
        <input 
          v-bind:disabled="this.answerSubmitted"
          type="radio" 
          name="options" 
          v-bind:value="answer"
          v-model="this.chosenAnswer">
        <label v-html="answer"></label><br/>
      </template>
  
      <button v-if="!this.answerSubmitted" v-on:click="this.submitAnswer()" class="send" type="button">Send</button>


      <section v-if="this.answerSubmitted" class="result">

        <h4 v-if="this.chosenAnswer == this.correctAnswer">
          &#9989; Booa!! Resposta CORRETA.
        </h4>
        <h4 v-else v-html="'&#10060; I`m sorry, resposta errada. A resposta correta é' + this.correctAnswer">
        </h4>
        
          <button v-on:click="this.getNewQuestion" class="send" type="button">Next question</button>
      </section>
    </template>

  </div>
</template>

<script>

import ScoreBoard from './components/ScoreBoard.vue';

export default {
    name: "App",
    components: {
      ScoreBoard
    },
    data() {
        return {
            question: undefined,
            incorretAnswers: undefined,
            correctAnswer: undefined,
            chosenAnswer: undefined,
            answerSubmitted: false,
            winCount: 0,
            loseCount: 0
        };
    },
    methods: {
        submitAnswer() {
            if (!this.chosenAnswer) { //se opcao de escolha estiver vazia
                alert("Escolha uma das opçoes");
            }
            else {
                this.answerSubmitted = true;
                if (this.chosenAnswer == this.correctAnswer) {
                    console.log("resposta CERTA");
                    this.winCount++
                }
                else {
                    console.log("resposta ERRADA");
                    this.loseCount++
                }
            }
        },
        getNewQuestion() {
            //reseta as perguntas ao clicar no botao next question
            this.answerSubmitted = false;
            this.chosenAnswer = undefined;
            this.question = undefined;
            this.axios
                .get("https://opentdb.com/api.php?amount=1&category=18")
                .then((response) => {
                this.question = response.data.results[0].question; //propriedade question, que vem da api
                this.incorretAnswers = response.data.results[0].incorrect_answers; //propriedade incorrect_answers, que vem da api
                this.correctAnswer = response.data.results[0].correct_answer; //propriedade correct_ans, que vem da api
            });
        }
    },
    computed: {
        answers() {
            var answers = JSON.parse(JSON.stringify(this.incorretAnswers));
            answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
            return answers;
        }
    },
    created() {
        this.getNewQuestion();
    },
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio]{
    margin: 12px 4px;
  }

  button.send{
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }

}
</style>
