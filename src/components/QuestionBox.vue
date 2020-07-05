<template>
  <div class="text-center">
    <b-jumbotron>
      <template v-slot:lead>{{ currentQuestion.question }}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="selectedAnswer(index)"
          :class="answerClass(index)"
        >{{ answer }}</b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex==null || answered"
      >submit</b-button>

      <b-button @click="nextQuestion" variant="success">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  props: {
    currentQuestion: Object,
    nextQuestion: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffelAnswers();
      }
    }
  },
  methods: {
    selectedAnswer(index) {
      this.selectedIndex = index;
    },
    shuffelAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffledAnswers = _.shuffle(answers); // _.shuffle comes from lodash libery
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    answerClass(index) {
      let answerClass = "";

      if (!this.answered && this.selectedIndex === index) {
        answerClass = "selected";
      } else if (this.answered && this.correctIndex === index) {
        answerClass = "correct";
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = "incorrect";
      }
      return answerClass;

      // [
      //           !answered && selectedIndex === index ? 'selected' :
      //           answered && correctIndex === index ? 'correct' :
      //           answered && selectedIndex === index && correctIndex !== index ? 'incorrect' : ''
      //           ]
    }
  }
};
</script>

<style scoped>
.list-group {
  margin-bottom: 20px;
}
.list-group-item:hover {
  background-color: #00008b80;
  color: ghostwhite;
  cursor: pointer;
}
.selected {
  background-color: #02026680;
  color: ghostwhite;
}

.correct {
  background-color: rgba(0, 128, 0, 0.623);
  color: ghostwhite;
}

.incorrect {
  background-color: rgba(194, 34, 34, 0.424);
  color: ghostwhite;
}
.btn {
  margin: 0 10px;
}
</style>
