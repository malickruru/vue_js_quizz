<template >
  <div v-if="etat_partie == 'debut'" class=" card w-1/2 bg-base-content text-base-300 ">
    <div class="card-body items-center text-center">
      <h2 class="card-title">Quizz géo &#127757; </h2>

      <div class="card-actions justify-end">
        <button class="btn " @click="start">Démarrer</button>

      </div>
    </div>

  </div>

  <Question v-if="etat_partie == 'en cours'" :key="question_active" :index="question_active"
    :question="questions[question_active]" @next="next" @score="incrementScore" />

  <div v-if="etat_partie == 'termine'" class=" card w-1/2 bg-base-content text-base-300 ">
    <div class="card-body items-center text-center">

      <h2 class="card-title">Good game, Votre score : {{ score }}</h2>

      <div class="card-actions justify-end">
        <button class="btn " @click="reStart">Recommencer</button>
      </div>
    </div>
  </div>
</template>
<script>
import Question from './components/Question.vue';
import { data } from './data/questions';

export default {
  components: { Question },
  data() {
    return {
      etat_partie: "debut",
      questions: [],
      question_active: 0,
      score: 0,
    };
  },
  methods: {
    start() {
      //selectioner une dizaine de questions aléatoire
      while (this.questions.length < 10) {
        var elementAleatoire = data[Math.floor(Math.random() * data.length)];
        if (!this.questions.includes(elementAleatoire)) {
          this.questions.push(elementAleatoire);
        }
      }
      // démarrer le quiz
      this.etat_partie = 'en cours'
    },
    next() {
      if (this.question_active == 9) {
        this.etat_partie = 'termine'
      } else {
        this.question_active++
      }

    },
    incrementScore() {
      this.score++
    },
    reStart() {
      this.questions = []
      this.score = 0
      this.question_active = 0
      this.start()
    }
  },


}
</script>
<style ></style>