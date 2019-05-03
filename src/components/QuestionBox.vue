<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">{{currentQuestion.question}}</template>

      <hr class="my-4">
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >{{ answer}}</b-list-group-item>
      </b-list-group>

      <b-button
        @click="submitAnswer"
        variant="primary"
        :disabled="selectedIndex == null || answered"
      >Submit</b-button>
      <b-button @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
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
      shuffledAndswers: [],
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
        this.shuffleAnswers();
        this.answered = false;
      }
    }
    //   this.selectedIndex = null;
    //   this.shuffleAnswers();
    // }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    submitAnswer() {
      let isCorrect = false;
      console.log(this.selectedIndex, this.correctIndex);
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffledAndswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAndswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    answerClass(index) {
      let answerClass = "";

      if (!this.answered && this.selectedIndex === index)
        answerClass = "selected";
      else if (this.answered && this.correctIndex === index)
        answerClass = "correct";
      else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      )
        answerClass = "incorrect";
      return answerClass;
    }
  }
};
</script>



<style scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item:hover {
  background-color: #eeeeee;
  cursor: pointer;
}
.btn {
  margin: 5px;
}
.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: red;
}
</style>
