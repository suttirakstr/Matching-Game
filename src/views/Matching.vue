<template>
  <div id="app">
    <div class="outer-wrapper">
      <h1 class="title">Matching Game</h1>
      <div class="row">
        <div class="wrapper col">
          <div class="start" v-if="state == 0">
            <button  class="btn-start" @click="shuffleCard">Start</button>
          </div>
          <div v-if="timeLeft == 0">
            <h1 class="timeout">หมดเวลา!! </h1>
          </div>

          <ul class="cards" v-if="state == 1 && timeLeft > 0">
            <li
              class="card"
              v-for="(card, index) in cards"
              :key="index"
              :class="{ flip: card.flipped }"
              @click="flipCard(index)"
            >
              <div class="view front-view">
                <img :src="imageUrl" alt="icon" />
              </div>
              <div class="view back-view">
                <img :src="card.image" alt="card-img" class="card-img" />
              </div>
            </li>
          </ul>
          <div class="details">
            <p class="time">
              เวลา:
              <span
                ><b>{{ timeLeft }}</b> วิ</span
              >
            </p>
            <p class="flips">
              จำนวนคลิก:
              <span
                ><b>{{ flips }}</b></span
              >
            </p>
            <p class="flips">
              ชนะ:
              <span
                ><b>{{ totalWins }}</b></span
              >
            </p>
            <button class="btn" @click="nextLevel" v-if="showNextLevelButton">
              เล่นด่านถัดไป
            </button>
            <button v-if="timeLeft === 0 " class="btn" @click="playAgain">
              เล่นอีกครั้ง
            </button>
            <button
              class="btn"
              @click="startNewGame"
              v-if="!isWinner && isPlaying"
            >
              เริ่มเล่นใหม่
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import "../css/style.css";
export default {
  name: "MatchingGames",
  data() {
    return {
      imageUrl: require("@/assets/img/viewcard.jpg"),
      state: 0,
      totalWins: 0,
      maxTime: 200,
      timeLeft: 200,
      flips: 0,
      matchedCard: 0,
      disableDeck: false,
      isPlaying: false,
      cardOne: null,
      cardTwo: null,
      timer: null,
      isWinner: false,
      showNextLevelButton: false,
    };
  },
  methods: {
    initTimer() {
      if (this.timeLeft <= 0) {
        clearInterval(this.timer);

        if (this.matchedCard === 9) {
          this.isWinner = true;
        } else {
          this.cards.forEach((card) => (card.clickable = false));
        }
        return;
      }
      this.timeLeft--;
    },
    flipCard(index) {
      if (!this.isPlaying) {
        this.isPlaying = true;
        this.timer = setInterval(this.initTimer, 1000);
      }
      const clickedCard = this.cards[index];
      if (
        clickedCard !== this.cardOne &&
        !this.disableDeck &&
        this.timeLeft > 0
      ) {
        this.flips++;
        clickedCard.flipped = true;

        if (!this.cardOne) {
          this.cardOne = clickedCard;
          return;
        }
        this.cardTwo = clickedCard;
        this.disableDeck = true;
        const cardOneImg = this.cardOne.image;
        const cardTwoImg = this.cardTwo.image;
        this.matchCards(cardOneImg, cardTwoImg);
        if (this.matchedCard === 9) {
          clearInterval(this.timer);
          this.showNextLevelButton = true;
          (this.disableDeck = false),
            (this.isPlaying = false),
            setTimeout(() => {}, 500);
        }
      }
      if (this.timeLeft <= 0) {
        clearInterval(this.timer);
        setTimeout(() => {}, 500);
      }
    },
    matchCards(img1, img2) {
      if (img1 === img2) {
        this.matchedCard++;

        if (this.matchedCard === 9 && this.timeLeft > 0) {
          clearInterval(this.timer);
        }
        if (this.cardOne && this.cardTwo) {
          this.cardOne.clickable = false;
          this.cardTwo.clickable = false;
          this.cardOne = this.cardTwo = null;
          this.disableDeck = false;
        }
      } else {
        if (this.cardOne && this.cardTwo) {
          setTimeout(() => {
            if (this.cardOne && this.cardTwo) {
              this.cardOne.shake = true;
              this.cardTwo.shake = true;
            }
          }, 800);
          setTimeout(() => {
            if (this.cardOne && this.cardTwo) {
              this.cardOne.shake = false;
              this.cardTwo.shake = false;
              this.cardOne.flipped = false;
              this.cardTwo.flipped = false;
              this.cardOne = this.cardTwo = null;
              this.disableDeck = false;
            }
          }, 800);
        }
      }
    },
    shuffleCard() {
      this.state = 1;
      this.timeLeft = this.maxTime;
      this.flips = this.matchedCard = 0;
      this.cardOne = this.cardTwo = null;
      clearInterval(this.timer);
      const images = [];
      for (let i = 1; i <= 9; i++) {
        images.push(require(`@/assets/imeg/img${i}.jpg`));
        images.push(require(`@/assets/imeg/img${i}.jpg`));
      }
      for (let i = images.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [images[i], images[j]] = [images[j], images[i]];
      }
      this.cards = images.map((image) => ({
        image,
        flipped: false,
        clickable: true,
        shake: false,
      }));
      this.disableDeck = this.isPlaying = this.isWinner = false;
    },

    startNewGame() {
      this.shuffleCard();
      this.maxTime = 200;
      this.timeLeft = 200;
      this.timer = setInterval(this.initTimer, 1000);
      this.isPlaying = true;
      this.shuffleCard();
      this.totalWins = 0;
      this.resetGame();
    },

    nextLevel() {
      this.isWinner = false;
      this.showNextLevelButton = true;
      this.maxTime -= 30;
      this.totalWins++;
      if (this.totalWins === 5) {
        alert("Congratulations! You've won 5 times!");
        this.totalWins = 0;
        this.shuffleCard();
        this.maxTime = 200;
        this.timeLeft = 200;
        this.timer = setInterval(this.initTimer, 1000);
        this.isPlaying = true;
        this.shuffleCard();
        this.totalWins = 0;
        this.resetGame();
      }

      this.shuffleCard();
      this.timeLeft = this.maxTime;
      this.flips = this.matchedCard = 0;
      clearInterval(this.timer);
      this.showNextLevelButton = false;
      this.timer = setInterval(this.initTimer, 1000);
      this.isPlaying = true;
    },

    playAgain() {
      this.shuffleCard();
      this.resetGame();
    },

    resetGame() {
      this.timeLeft = this.maxTime;
      this.flips = this.matchedCard = 0;
      this.cardOne = this.cardTwo = null;
      clearInterval(this.timer);
      this.cards.forEach((card) => {
        card.flipped = false;
        card.clickable = true;
        card.shake = false;
      });

      this.disableDeck = this.isPlaying = this.isWinner = this.showNextLevelButton = false;
    },
    mounted() {
      this.shuffleCard();
    },
    watch: {
      isWinner(newValue) {
        if (newValue) {
          this.showNextLevelButton = true;
          this.nextLevelLogic();
        }
      },
   
    },
  },
};
</script>
