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
          <h2 class="question">{{ item.question }}</h2>
          <img
            v-if="item.image"
            :src="item.image"
            alt=""
            height="200"
            width="200"
          />
          <div
            class="choice-button__group"
            v-for="(choiceItem, ind) in item.choices"
            :key="ind"
          >
            <button
              class="choice-button"
              :disabled="item.isQuestionAnswered"
              :class="choiceItem.class"
              @click="checkAnswer(choiceItem.choice, index, ind)"
            >
              <p>{{ choiceItem.choice }}</p>
              <img
                v-if="choiceItem.class == 'correctAnswer'"
                :src="TickIcon"
                alt=""
                height="20"
                width="20"
              />
              <img
                v-if="choiceItem.class == 'wrongAnswer'"
                :src="CrossIcon"
                alt=""
                height="20"
                width="20"
              />
            </button>
          </div>
          <button
            class="next-button"
            v-if="item.isQuestionAnswered"
            @click="gotoNextQuestion(index)"
          >
            Next <span class="arrow">></span>
          </button>
        </div>
      </div>
      <div v-if="showResult">
        <img :src="ResultImage" alt="" class="result-image" />
        <h1>Result</h1>
        <p class="result-body">
          You got {{ quizResult.correct }} correct answers
        </p>
        <button @click="tryAgain()" class="try-again-button">Try again</button>
      </div>
    </div>
  </div>
</template>

<script>
import ResultImage from "../assets/images/undraw_winners_re_wr1l 1.png";
import TickIcon from "../assets/images/tick-icon.jpg";
import CrossIcon from "../assets/images/cross-icon.jpg";
export default {
  name: "Quiz",
  props: {},

  data() {
    return {
      ResultImage: ResultImage,
      TickIcon: TickIcon,
      CrossIcon: CrossIcon,
      showResult: false,
      imageUrl: null,
      questions: [
        {
          id: 1,
          question: "Which breed is Freckles?",
          choices: [
            { choice: "A. Weimaraner", class: "answer" },
            { choice: "B. Australian Cattledog", class: "answer" },
            { choice: "C. Border Collie", class: "answer" },
            { choice: "D. Pembroke", class: "answer" },
          ],
          image: null,
          correctAnswer: "B. Australian Cattledog",
          visibilty: true,
          isQuestionAnswered: false,
          answeredValue: null,
        },
        {
          id: 2,
          question: "What purpose of the cattledog breed for?",
          choices: [
            { choice: "A. To protect people", class: "answer" },
            { choice: "B. To herd cattle", class: "answer" },
            { choice: "C. To protect cattle", class: "answer" },
            { choice: "D. To hunt", class: "answer" },
          ],
          correctAnswer: "C. To protect cattle",
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

      if (
        this.questions.length - 1 === index ||
        this.questions[index].correctAnswer !==
          this.questions[index].answeredValue
      ) {
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
}

.quiz-container {
  margin: 1rem auto;
  max-width: 700px;
}
.headline {
  color: #ffffff;
  font-size: 35px;
}
.bg-image1 {
  display: none;
  position: static;
}
.bg-image2 {
  display: none;
  position: static;
}
.question-card {
  background-color: #ffffff;
  border-radius: 24px;
  max-width: 400px;
  height: 550px;
  margin: 0 auto;
}
.answer {
  font-size: 17px;
  width: 300px;
  height: 40px;
  border-width: 1px;
  color: rgba(29, 28, 28, 0.555);
  border-color: gray;
  font-weight: bold;
  border-radius: 8px;
  background: #fff;
  margin-top: 10px;
}

.answer:hover {
  cursor: pointer;
  background: linear-gradient(#dad6cb, #e0ddd0);
}
.next-button {
  margin-top: 20px;
  margin-left: 200px;
  font-size: 15px;
  width: 117px;
  height: 46px;
  border-width: 0px;
  color: #ffffff;
  border-color: #337fed;
  font-weight: bold;
  border-radius: 42px;
  box-shadow: inset 0px 1px 0px 0px #97c4fe;
  text-shadow: inset 0px 1px 0px #1570cd;
  background: linear-gradient(#3d94f6, #1e62d0);
}
.next-button:hover {
  background: linear-gradient(#1e62d0, #3d94f6);
  cursor: pointer;
}
.arrow {
  margin-left: 8px;
}

.correctAnswer {
  background-color: rgb(46, 124, 46);
  color: white;
  font-size: 17px;
  width: 300px;
  height: 40px;
  border-width: 1px;
  border-color: gray;
  font-weight: bold;
  border-radius: 8px;
  margin-top: 10px;
}

.wrongAnswer {
  background-color: #d33434;
  color: white;
  font-size: 17px;
  width: 300px;
  height: 40px;
  border-width: 1px;
  border-color: gray;
  font-weight: bold;
  border-radius: 8px;
  margin-top: 10px;
}

.result-image {
  margin-top: 20px;
  margin-bottom: 25px;
}

.result-body {
  font-size: 20px;
  margin-bottom: 40px;
}

.try-again-button {
  border: 1px solid blue;
  border-radius: 30px;
  background-color: white;
  color: blue;
  padding: 14px 35px;
  font-size: 18px;
  cursor: pointer;
  margin-top: 20px;
}
.try-again-button:hover {
  background: #2196f3;
  color: white;
}
.choice-button__group {
  display: flex;
  justify-content: center;
}
.choice-button {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
}
.question {
  padding-top: 15px;
}
@media screen and (max-width: 768px) {
  .question {
    font-size: 22px;
  }
}
</style>
