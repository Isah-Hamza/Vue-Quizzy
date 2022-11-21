<script setup>
import { onMounted, ref } from 'vue';
import QuizEndedOverlay from '../components/QuizEndedOverlay.vue';
import LoadingState from '../components/LoadingState.vue';
import ProgressBar from '../components/ProgressBar.vue';
import QuestionContainer from '../components/QuestionContainer.vue';
import Option from '../components/Option.vue';
import Header from '../components/Header.vue';
import Timer from '../components/Timer.vue';

// data
const gameEnded = ref(false);
let canClick = ref(true);
let questionCounter = ref(0);
const timer = ref(100);
const userScore = ref(0)
let interval;
const currentQuestion = ref({
  question: '',
  answer: 1,
  choices: []
});

let questions = ref([]);

//  methods
let options = []

const optionSelected = (element) => {
  if (element) {
    options.push(element)
  }
}

const onOptionSelected = (idx) => {
  if (!canClick.value) return;
  canClick.value = false;
  clearInterval(interval);
  const optionContainer = options[idx];
  const optionID = idx + 1;
  if (currentQuestion.value.answer == optionID) {
    userScore.value += 10;
    optionContainer.children[0].classList.remove('option-default');
    optionContainer.children[0].classList.add('option-correct');
  } else {
    optionContainer.children[0].classList.remove('option-default');
    optionContainer.children[0].classList.add('option-wrong');
  }
  setTimeout(() => {
    loadQuestion();
  }, 2000);
}

const loadQuestion = () => {
  gameEnded.value = false;
  countDownTimer()
  canClick.value = true;
  if (options.length) {
    options.forEach(option => {
      option.children[0].classList.add('option-default');
      option.children[0].classList.remove('option-correct');
      option.children[0].classList.remove('option-wrong');
    });
  }

  //check for more questions
  if (questions.value.length > questionCounter.value) {
    //load questions
    currentQuestion.value = questions.value[questionCounter.value];
    questionCounter.value++;
  } else {
    gameEnded.value = true;
    clearInterval(interval);
  }
}

const countDownTimer = () => {
  timer.value = 100;
  interval = setInterval(() => {
    timer.value > 0 ?
      timer.value-- :
      (clearInterval(interval), loadQuestion())
  }, 150);
}

const restartGame = () => {
  userScore.value = 0;
  questionCounter.value = 0;
  gameEnded.value = false;
  fetchRemoteData()
}

const fetchRemoteData = async () => {
  await fetch('https://opentdb.com/api.php?amount=10&category=18&type=multiple')
    .then(res => res.json())
    .then(data => {
      const returnedQuestions = data.results;
      const newQuestions = returnedQuestions.map((item) => {
        const transformedQuestion = {}
        transformedQuestion.question = item.question;
        transformedQuestion.choices = item.incorrect_answers;
        transformedQuestion.answer = Math.floor(Math.random() * 4 + 1);
        transformedQuestion.choices.splice(transformedQuestion.answer - 1, 0, item.correct_answer)
        return transformedQuestion
      });
      questions.value = newQuestions;
      loadQuestion();
    })
    .catch(err => console.log(err));

}

// life cycle hooks
onMounted(() => {
  fetchRemoteData();
});

</script>

<template>
  <main class="bg-gray-100 min-h-screen grid place-content-center py-5">
    <div v-if="questions.length > 0"
      class="grid w-screen sm:max-w-sm shadow-md bg-gray-200 sm:rounded-lg mx-auto px-6 py-10 h-screen sm:h-auto min-h-[600px] overflow-y-auto bg-pattern">
      <!-- gameOver overlay -->
      <QuizEndedOverlay v-if="gameEnded" :score="userScore" :restart-game="restartGame" />
      <div>
        <Header :score="userScore" />
        <!-- timer -->
        <Timer :timer="timer" />
      </div>
      <div>
        <!-- question container -->
        <QuestionContainer :curren-question="currentQuestion" />
        <!-- option container -->
        <section>
          <div v-for="(choice, idx) in currentQuestion.choices" :key="idx" @click="onOptionSelected(idx)"
            :ref="optionSelected">
            <Option :choice="choice" :idx="idx" :key="idx" />
          </div>
        </section>
        <!-- progress -->
        <ProgressBar :questions="questions" :question-counter="questionCounter" />
      </div>
    </div>
    <LoadingState v-else />
  </main>
</template>

<style scoped>
.neumorphic {
  box-shadow: 6px 6px 18px rgba(0, 0, 0, 0.44), 6px 6px 18px #ffffff;
}

.bg-pattern {
  background-image: url(../assets/images/christmas-background.webp);
}
</style>
