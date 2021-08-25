<template>
  <div>
    <div class="cards-wrapper">
      <div
        v-for="(image, key) in cards"
        :key="key"
        class="cards"
        @click="selectCard(key)"
      >
        <img
          v-if="isCardsShown || userAnswers[key]"
          :src="require(`../../assets/images/${image}.png`)"
        />
        <span v-else>?</span>
      </div>
    </div>
    <div class="description-box">
      <div v-if="gameStatus === 'ready'">
        <p>
          This is a very simple memory game.
          You should memorize images and then select the cards of the given word.
          Press start button when you are ready.
        </p>
        <button @click="prepareData">start</button>
      </div>
      <div v-else-if="gameStatus === 'memorizing'">
        <p>
          You have
          <Counter @finished="startTheGame" key="timeToStart" />
          seconds to memorize the image of each card.
        </p>
      </div>
      <div v-else-if="gameStatus === 'started'">
        <p>
          You have
          <Counter @finished="finishTheGame" key="timeToFinish" />
          seconds to select all the cards that contains
          {{ selectedWord }}
          image.
        </p>
      </div>
      <div v-else>
        <p>
          You have selected
          {{ Object.values(userAnswers).filter((answer) => answer === 'right').length }}
          right answers and
          {{ Object.values(userAnswers).filter((answer) => answer === 'wrong').length }}
          wrong answers from
          {{ rightAnswersPossibleCount }}
          right answers possible.
        </p>
        <button @click="prepareData">again</button>
      </div>
    </div>
  </div>
</template>

<script>
import Counter from '../counter/Counter.vue';
import './game.scss';

export default {
  name: 'Game',
  components: { Counter },
  data() {
    return {
      gameStatus: 'ready',
      cards: {
        1: null,
        2: null,
        3: null,
        4: null,
        5: null,
        6: null,
        7: null,
        8: null,
        9: null,
      },
      isCardsShown: false,
      selectedWord: null,
      userAnswers: {},
      rightAnswersPossibleCount: 0,
    };
  },
  methods: {
    prepareData() {
      this.gameStatus = 'memorizing';
      this.isCardsShown = false;
      this.userAnswers = {};
      const imagesArray = ['car', 'dog', 'human'];
      Object.keys(this.cards).forEach((key) => {
        this.$set(this.cards, key, imagesArray[Math.floor(Math.random() * 3)]);
      });
      this.selectedWord = imagesArray[Math.floor(Math.random() * 3)];
      this.rightAnswersPossibleCount =
        Object.values(this.cards).filter((word) => word === this.selectedWord).length;
      if (this.rightAnswersPossibleCount === 0) {
        this.prepareData();
        return;
      }
      this.isCardsShown = true;
    },
    startTheGame() {
      this.isCardsShown = false;
      this.gameStatus = 'started';
    },
    selectCard(key) {
      if (this.isCardsShown) return;
      this.$set(
        this.userAnswers,
        key,
        this.cards[key] === this.selectedWord ? 'right' : 'wrong',
      );
      const userRightAnswersCount =
        Object.values(this.userAnswers).filter((answer) => answer === 'right').length;
      if (this.rightAnswersPossibleCount === userRightAnswersCount) this.finishTheGame();
    },
    finishTheGame() {
      this.gameStatus = 'finished';
      this.isCardsShown = true;
    },
  },
};
</script>
