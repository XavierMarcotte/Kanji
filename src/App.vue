<template>
  <h1>Guess the kanji</h1>
  <p>
    We generate a random dfficulty, this test is usefull only if you know many
    kanji. You can cheat by watching the console to see the meanings (left-click
    on the page and click on "inspect", then go to "console")
  </p>
  <p>!Warning! Write only in <strong>LOWERCASE.</strong></p>
  <p>The difficulty is : {{ getDifficulty }}</p>
  <p>What's that kanji {{ randomKanjiFromIndex }} ?</p>
  <form action="" @submit.prevent="check" class="mb-3">
    <input
      v-model="response"
      type="text"
      placeholder="Your answer"
      class="form-control answer"
      id="exampleInputPassword1"
    />
    <p v-if="goodResponse === false" class="text-danger">
      Wrong answer.. Try again
    </p>
    <button
      type="submit"
      class="btn btn-primary"
      :disabled="response.length === 0"
    >
      Check Answer
    </button>
  </form>
  <button
    v-if="falseresponse > 1"
    @click="showAnswer = true"
    type="submit"
    class="btn btn-primary"
  >
    Show the answer
  </button>
  <div class="div_responses" v-if="goodResponse || showAnswer" or>
    <p v-if="goodResponse" class="text-success">
      Good answer ! You could also have answered :
    </p>
    <p v-if="showAnswer">
      The answer(s) for {{ randomKanjiFromIndex }} was (were) :
    </p>
    <ul>
      <li v-for="meaning in kanjiMeanings">
        <p>- {{ meaning }}</p>
      </li>
    </ul>
  </div>
  <button @click="reloadPage" type="button" class="btn btn-outline-primary">
    Try another one
  </button>
</template>
<script setup>
import { computed, onMounted, ref, watch } from "vue";
import kanjiByDiffuclty from "../data/kanji.js";
const showAnswer = ref(false);
// console.log(showAnswer);

const difficulty = ref(["easy", "medium", "hard", "impossible"]);
function getRandomInt(max) {
  return Math.floor(Math.random() * max);
}

// To get a random difficulty
const randomForChart = ref(getRandomInt(difficulty.value.length));
const randomKanji = ref(difficulty.value[randomForChart.value]);
const getDifficulty = randomKanji.value;
// console.log(getDifficulty);

const difficultyObject = ref({
  easy: [1, 2, 3, 4],
  medium: [5, 6, 7, 8, 9, 10, 11, 12, 13],
  hard: [14, 15, 16, 17],
  impossible: [18, 19, 20, 21, 22],
});

// Get the array corresponding to the random difficulty
const getNumberDiffculty = ref(difficultyObject.value[getDifficulty]);
// console.log(getNumberDiffculty.value);

// Get a random number index within the selected difficulty array
const randomNumberAmongDiffculty = ref(
  getRandomInt(getNumberDiffculty.value.length)
);
// console.log(randomNumberAmongDiffculty.value);

// Get the actual number corresponding to the random index within the selected difficulty
const randomKanjiDiffcultyNumber = ref(
  getNumberDiffculty.value[randomNumberAmongDiffculty.value]
);
// console.log(randomKanjiDiffcultyNumber.value);

//Get all kanji from the number we get before
const getKanji = kanjiByDiffuclty[randomKanjiDiffcultyNumber.value];
// console.log(getKanji);
const randomKanjiIndex = getRandomInt(getKanji.length);
const randomKanjiFromIndex = getKanji[randomKanjiIndex];
// console.log(randomKanjiFromIndex);

//Object of the random kanji
const kanjiMeanings = ref();
onMounted(() => {
  fetch(`https://kanjiapi.dev/v1/kanji/${randomKanjiFromIndex}`)
    .then((response) => response.json())
    .then((data) => {
      kanjiMeanings.value = data.meanings;
      console.log("Kanji meanings :", kanjiMeanings.value);
    })
    .catch((error) => {
      // console.error("Erreur lors de l'appel API :", error);
    });
});

const response = ref("");
const goodResponse = ref();
const falseresponse = ref(0);
const check = () => {
  for (let meaning of kanjiMeanings.value) {
    if (meaning === response.value) {
      goodResponse.value = true;
      // console.log("Bonne réponse");
      return;
    }
  }
  goodResponse.value = false;
  falseresponse.value++;
  // console.log(falseresponse);
  // console.log("Mauvaise réponse");
};

const reloadPage = () => {
  window.location.reload();
};
</script>

<style scoped>
button {
  margin-top: 1rem;
}
li {
  list-style: none;
}
.answer {
  margin-bottom: 1rem;
}
form,
.div_responses,
ul {
  display: flex;
  margin: auto;
  justify-content: center;
  flex-direction: column;
}
</style>
