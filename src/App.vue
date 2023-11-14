<script>
import ScoreBoard from './components/ScoreBoard.vue'

export default {
  name: 'App',
  components: {
    ScoreBoard,
  },
  data() {
    return {
      incorrectAnswers: [],
      correctAnswer: undefined,
      question: undefined,
      chosenAnswer: null,
      answerSubmitted: false,
      winAnswers: 0,
      loseAnswers: 0
    }
  },
  methods: {
    submitAnswer() {
      if (!this.chosenAnswer) {
        alert('É necessário selecionar alguma alternativa.')
      } else {
        this.answerSubmitted = true
        if (this.chosenAnswer == this.correctAnswer) {
          this.winAnswers++
          localStorage.setItem("CorrectAnswers", JSON.stringify(this.winAnswers))
        } else {
          this.loseAnswers++
          localStorage.setItem("IncorrectAnswers", JSON.stringify(this.loseAnswers))
        }
      }
    },
    getNewQuestion() {
      this.axios
        .get('https://opentdb.com/api.php?amount=1&category=18')
        .then((response) => {
          this.question = response.data.results[0].question
          this.correctAnswer = response.data.results[0].correct_answer
          this.incorrectAnswers = response.data.results[0].incorrect_answers

          this.chosenAnswer = null
          this.answerSubmitted = false
        })
    },
    resetScore() {
      localStorage.setItem("IncorrectAnswers", JSON.stringify(0))
      localStorage.setItem("CorrectAnswers", JSON.stringify(0))
      this.winAnswers = 0
      this.loseAnswers = 0
    }
  },
  computed: {
    answers() {
      let answers = [...this.incorrectAnswers]
      answers.splice(Math.round(Math.random() * 4), 0, this.correctAnswer)
      return answers
    },
  },
  created() {
    this.getNewQuestion()
    this.winAnswers = JSON.parse(localStorage.getItem("CorrectAnswers")) ? JSON.parse(localStorage.getItem("CorrectAnswers")) : this.winAnswers
    this.loseAnswers = JSON.parse(localStorage.getItem("IncorrectAnswers")) ? JSON.parse(localStorage.getItem("IncorrectAnswers")) : this.loseAnswers
  },
};
</script>

<template>
  <div>
    <ScoreBoard @reset-score="resetScore()" :winAnswers="this.winAnswers" :loseAnswers="this.loseAnswers" />

    <h1 v-html="this.question"></h1>

    <div class="wrapper" v-for="(answer, index) in this.answers" :key="index">
      <input :id="index" type="radio" name="options" :value="answer" v-model="this.chosenAnswer"
        :disabled="answerSubmitted">
      <label :for="index" v-html="answer"></label>
    </div>

    <button class="send" type="button" @click="submitAnswer()">Send</button>

    <section v-if="this.answerSubmitted">
      <h4 v-if="this.chosenAnswer == this.correctAnswer"
        v-html="`&#9989; Parabéns você acertou! A alternativa '${ this.correctAnswer }' está correta!`"></h4>

      <h4 v-else v-html="`&#10060; Que pena, você errou! A alternativa correta é '${ this.correctAnswer }'.`"></h4>

      <button class="send" type="button" @click="getNewQuestion()">Next question</button>
    </section>
  </div>
</template>

<style lang="scss">
#app {
  max-width: 960px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin: 60px auto;
  color: #2c3e50;

  .wrapper {
    display: flex;
    flex-direction: row;
    margin: 12px 4px;
    user-select: none;
  }

  div {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
  }

  button.send {
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
