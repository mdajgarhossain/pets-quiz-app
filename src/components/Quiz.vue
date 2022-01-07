<template>
  <div class="quiz-container">
    <!-- Headline -->
    <h1 class="headline">Pets Quiz</h1>
    <!-- Background images -->
    <figure class="bg-image1">
      <img src="@/assets/images/Rectangle 1.png" alt="" />
    </figure>
    <figure class="bg-image2">
      <img src="@/assets/images/Rectangle 2.png" alt="" />
    </figure>
    <!-- Question -->
    <div class="question-card">
      <div v-for="(item, index) in questions" :key="item.id">
        <div v-if="item.visibilty">
          <h2>{{ item.question }}</h2>
          <img
            v-if="item.image"
            :src="item.image"
            alt=""
            height="200"
            width="200"
          />
          <div v-for="(choiceItem, ind) in item.choices" :key="ind">
            <button
              :disabled="item.isQuestionAnswered"
              :class="choiceItem.class"
              @click="checkAnswer(choiceItem.choice, index, ind)"
            >
              {{ choiceItem.choice }}
            </button>
          </div>
          <button
            v-if="item.isQuestionAnswered"
            @click="gotoNextQuestion(index)"
          >
            Next
          </button>
        </div>
      </div>
      <div v-if="showResult">
        <span>Correct answer: {{ quizResult.correct }}</span>
        <button @click="tryAgain()">Try again?</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Quiz",
  props: {},

  data() {
    return {
      showResult: false,
      imageUrl: null,
      questions: [
        {
          id: 1,
          question: "Which breed is Freckles?",
          choices: [
            { choice: "test1", class: "answer" },
            { choice: "test2", class: "answer" },
            { choice: "test3", class: "answer" },
            { choice: "test4", class: "answer" },
          ],
          image: null,
          correctAnswer: "test3",
          visibilty: true,
          isQuestionAnswered: false,
          answeredValue: null,
        },
        {
          id: 2,
          question: "What purpose of the cattledog breed for?",
          choices: [
            { choice: "test1", class: "answer" },
            { choice: "test2", class: "answer" },
            { choice: "test3", class: "answer" },
            { choice: "test4", class: "answer" },
          ],
          correctAnswer: "test2",
          visibilty: false,
          isQuestionAnswered: false,
          answeredValue: null,
        },
      ],
    };
  },

  created() {
    this.fetchImages();
  },

  computed: {
    quizResult() {
      let result = {
        correct: 0,
        wrong: 0,
        totalAnswered: 0,
        totalQuestions: this.questions.length,
      };
      this.questions.forEach((el) => {
        if (el.isQuestionAnswered) {
          if (el.correctAnswer === el.answeredValue) {
            result.correct++;
          } else {
            result.wrong++;
          }
          result.totalAnswered++;
        }
      });
      return result;
    },
  },

  methods: {
    async fetchImages() {
      this.loading = true;
      let response = await fetch(
        "https://dog.ceo/api/breed/cattledog/australian/images/random"
      );
      let jsonResponse = await response.json();
      if (jsonResponse && jsonResponse.message) {
        this.questions[0].image = jsonResponse.message;
      }
    },
    checkAnswer(answer, index, choiceIndex) {
      const question = this.questions[index];
      if (question.correctAnswer === answer) {
        question.choices[choiceIndex].class = "correctAnswer";
      } else {
        question.choices[choiceIndex].class = "wrongAnswer";
        question.choices.forEach((el) => {
          if (el.choice === question.correctAnswer) {
            el.class = "correctAnswer";
          }
        });
      }
      question.isQuestionAnswered = true;
      question.answeredValue = answer;
    },

    gotoNextQuestion(index) {
      this.questions[index].visibilty = false;

      if (this.questions.length - 1 === index) {
        this.showResult = true;
        this.questions[index].visibilty = false;
      } else {
        this.questions[index + 1].visibilty = true;
      }
    },
    tryAgain() {
      this.showResult = false;
      this.questions[0].visibilty = true;
      this.questions.forEach((data) => {
        data.choices = data.choices.map((item) => {
          return { ...item, class: "answer" };
        });
      });
      this.questions = this.questions.map((item) => {
        return {
          ...item,
          isQuestionAnswered: false,
          answeredValue: null,
        };
      });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
body {
  background-color: #585b89;
  position: relative;
  overflow-x: hidden;
}

.quiz-container {
  margin: 1rem auto;
  padding: 1.5rem;
  max-width: 700px;
}
.headline {
  color: #ffffff;
}
.bg-image1 {
  top: -240px;
  right: -250px;
  position: absolute;
}
.bg-image2 {
  top: 24px;
  left: -45px;
  position: absolute;
}
.question-card {
  background-color: #ffffff;
  border-radius: 24px;
  width: 400px;
  height: 550px;
  position: absolute;
  left: 488px;
  top: 107px;
}
.answer {
  padding: 10px;
  border: none;
  border-radius: 0.1rem;
  margin-bottom: 1rem;
}

.correctAnswer {
  background-color: green;
  color: white;
}

.wrongAnswer {
  background-color: red;
  color: white;
}
</style>
