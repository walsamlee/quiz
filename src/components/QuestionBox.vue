<template>
  <div>
    <b-jumbotron>
      <template v-slot:lead>
        <p>{{ currentQuestion.question }}</p>
      </template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item
           v-for="(answer, index) in answers"
           :key="index"
           @click="selectAnswer(index)"
           :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
      >
        Submit
      </b-button>
      <b-button @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    }
  },
  computed: {
    answers() {
      return [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffAnswers()
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index
    },
    shuffAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    submitAnswer() {
      let isCorrect = false

      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }

      this.answered = true
      this.increment(isCorrect)
    },
    answerClass(index) { 
      return (
        !this.answered && this.selectedIndex === index ? 'selected' :
        this.answered && this.correctIndex === index ? 'correct' :
        this.answered && this.selectedIndex === index && this.correctIndex !== index ? 'incorrect' : ''
      )
    }
  }
}
</script>

<style scoped>
  .list-group {
    margin-bottom: 15px;
  }

  .list-group-item {
    cursor: pointer;
  }

  .list-group-item:hover {
    background-color: #eeeeee;
  }

  .btn {
    margin: 0 5px;
  }

  .selected {
    background-color: #354a85;
    color: #ffffff;
  }

  .selected:hover {
    background-color: #6277af;
  }

  .correct {
    background-color: green;
    color: #ffffff;
  }

  .incorrect {
    background-color: red;
    color: #ffffff;
  }

  button:disabled {
    cursor: not-allowed;
  }
</style>
