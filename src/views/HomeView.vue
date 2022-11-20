<script setup>
import { onMounted, ref } from 'vue';

// data
const gameEnded = ref(false);
let canClick = ref(true);
let questionCounter = ref(0);
const timer = ref(100);
const userScore = ref(0)
let interval;
const currenQuestion = ref({
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
  if (currenQuestion.value.answer == optionID) {
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
    currenQuestion.value = questions.value[questionCounter.value];
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
      const newQuestions = returnedQuestions.map((item, idx) => {
        const transformedQuestion = {}
        transformedQuestion.question = item.question;
        transformedQuestion.choices = item.incorrect_answers;
        transformedQuestion.answer = Math.floor(Math.random() * 4 + 1);
        transformedQuestion.choices.splice(transformedQuestion.answer - 1, 0, item.correct_answer)
        return transformedQuestion
      });
      questions.value = newQuestions;
      console.log(newQuestions)
      loadQuestion();
    })
    .catch(err => console.log(err));

}

// life cycle hooks
onMounted(() => {
  // loadQuestion();
  fetchRemoteData();
});

</script>

<template>
  <main v-if="questions.length > 0" class="bg-gray-100 min-h-screen grid place-content-center">
    <div
      class="grid w-screen sm:max-w-sm shadow-md bg-gray-200 sm:rounded-lg mx-auto px-6 py-10 h-screen sm:h-auto min-h-[600px] bg-pattern">
      <!-- gameOver overlay -->
      <div v-if="gameEnded" class="fixed inset-0 z-10 bg-black/50 grid place-content-center">
        <div class="bg-green-400 rounded-md p-10 text-center w-60">
          <p class="text-xl font-semibold text-white">Game Over</p>
          <div class="text-lg flex justify-center items-center gap-2 text-white text-center">
            <p>Your Score is:</p>
            <p>{{ userScore }}</p>
          </div>
          <div class="flex gap-5 mt-6 justify-center">
            <button @click="restartGame" class="px-10 py-2 bg-blue-500 text-white rounded">Restart</button>
          </div>
        </div>
      </div>
      <div>
        <div class="flex items-center justify-between">
          <!-- title -->
          <p class="text-lg font-bold text-black">QUIZZY</p>
          <!-- score -->
          <div class="text-sm bg-white rounded-md px-5 py-1 w-fit flex items-center gap-2 ml-auto">
            <p>SCORE:</p>
            <span class="text-lg font-bold">{{ userScore }}</span>
          </div>
        </div>
        <!-- timer -->
        <div class="w-full bg-white h-2 rounded-full my-5 mb-16 sm:mb-5">
          <div class="bg-gray-500 rounded-3xl h-2" :style="`width:${timer}%`"></div>
        </div>
      </div>
      <div>
        <!-- question container -->
        <div class="rounded-lg p-2 mb-10 neumorphic">
          <div class="bg-white p-5 text-center">
            <p class='font-semibold'>{{ currenQuestion.question }}
            </p>
          </div>
        </div>
        <!-- option container -->
        <section>
          <div v-for="(choice, idx) in currenQuestion.choices" :key="idx" @click="onOptionSelected(idx)"
            :ref="optionSelected">
            <div
              class="option-default relative p-2 py-1 font-bold flex items-center gap-5 rounded-md shadow mt-4 bg-green-600 text-white cursor-pointer">
              <!-- bonus -->
              <div class="absolute bg-blue-500 text-white right-0 top-0 p-2 rounded rotate-45">
                <p class="font-bold -rotate-45 text-sm">+10</p>
              </div>
              <!-- option id -->
              <div class="bg-gray-600 text-white p-3 px-4 rounded">{{ idx }}</div>
              <!-- option text -->
              <div class="font-semibold">{{ choice }}</div>
            </div>
          </div>
        </section>
        <!-- progress -->
        <div class="grid gap-1 justify-center text-center mt-7">
          <p class="text-sm">
            <span class="font-bold">{{ questionCounter }}</span>
            <span class="font-bold">/</span>
            <span class="font-bold">{{ questions.length }}</span>
          </p>
          <div class="w-16 h-1.5 rounded bg-black"></div>
        </div>
      </div>
    </div>
  </main>
  <div
    class="grid place-content-center w-screen sm:max-w-sm shadow-md bg-gray-200 sm:rounded-lg mx-auto px-6 py-10 h-screen sm:h-auto min-h-[600px]"
    v-else>Loading... </div>
</template>

<style>
.neumorphic {
  box-shadow: 6px 6px 18px rgba(0, 0, 0, 0.44), 6px 6px 18px #ffffff;
}

.bg-pattern {
  background-image: url(../assets/images/christmas-background.webp);
}

.option-default {
  @apply !bg-white !text-black
}

.option-default>div:first-child {
  @apply hidden
}

.option-correct {
  @apply !bg-green-500 !text-white
}

.option-correct>div:first-child {
  @apply block
}

.option-wrong {
  @apply !bg-red-500 !text-white
}

.option-wrong>div:first-child {
  @apply hidden
}
</style>
