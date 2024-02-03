<template>
  <div id="app">
    <div class="outer-wrapper">
      <h1 class="title">Matching Game</h1>
      <div class="row ">
        
        <div class="wrapper col ">
          <ul class="cards">
            <li
              class="card"
              v-for="(card, index) in cards"
              :key="index"
              :class="{ flip: card.flipped }"
              @click="flipCard(index )"
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
                ><b>{{ timeLeft }}</b
                > วิ</span
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
            <button v-if="timeLeft === 0" class="btn" @click="playAgain" >
              เล่นอีกครั้ง
            </button>
            <button class="btn " @click="startNewGame" v-if="!isWinner && isPlaying">
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
      cards: [
        { id: 1, image: require("@/assets/imeg/img1.jpg"), flipped: false },
        { id: 2, image: require("@/assets/imeg/img2.jpg"), flipped: false },
        { id: 3, image: require("@/assets/imeg/img3.jpg"), flipped: false },
        { id: 4, image: require("@/assets/imeg/img4.jpg"), flipped: false },
        { id: 5, image: require("@/assets/imeg/img5.jpg"), flipped: false },
        { id: 6, image: require("@/assets/imeg/img6.jpg"), flipped: false },
        { id: 7, image: require("@/assets/imeg/img1.jpg"), flipped: false },
        { id: 8, image: require("@/assets/imeg/img2.jpg"), flipped: false },
        { id: 9, image: require("@/assets/imeg/img3.jpg"), flipped: false },
        { id: 10, image: require("@/assets/imeg/img4.jpg"), flipped: false },
        { id: 11, image: require("@/assets/imeg/img5.jpg"), flipped: false },
        { id: 12, image: require("@/assets/imeg/img6.jpg"), flipped: false },
        { id: 13, image: require("@/assets/imeg/img7.jpg"), flipped: false },
        { id: 14, image: require("@/assets/imeg/img8.jpg"), flipped: false },
        { id: 15, image: require("@/assets/imeg/img7.jpg"), flipped: false },
        { id: 16, image: require("@/assets/imeg/img8.jpg"), flipped: false },
        { id: 17, image: require("@/assets/imeg/img9.jpg"), flipped: false },
        { id: 18, image: require("@/assets/imeg/img9.jpg"), flipped: false },
   
      ],
      totalWins: 0,
      maxTime: 250,
      timeLeft: 250,
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
          this.disableDeck = false,
      this.isPlaying = false,
          setTimeout(() => {
          }, 500);
        }
      }
      if (this.timeLeft <= 0) {
        clearInterval(this.timer);
        setTimeout(() => {
        }, 500);
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
          }, 400);
          setTimeout(() => {
            if (this.cardOne && this.cardTwo) {
              this.cardOne.shake = false;
              this.cardTwo.shake = false;
              this.cardOne.flipped = false;
              this.cardTwo.flipped = false;
              this.cardOne = this.cardTwo = null;
              this.disableDeck = false;
            }
          }, 1250);
        }
      }
    },
    shuffleCard() {
      this.timeLeft = this.maxTime;
      this.flips = this.matchedCard = 0;
      this.cardOne = this.cardTwo = null;
      clearInterval(this.timer);
      const images = [
        require("@/assets/imeg/img1.jpg"),
        require("@/assets/imeg/img2.jpg"),
        require("@/assets/imeg/img3.jpg"),
        require("@/assets/imeg/img4.jpg"),
        require("@/assets/imeg/img5.jpg"),
        require("@/assets/imeg/img6.jpg"),
        require("@/assets/imeg/img1.jpg"),
        require("@/assets/imeg/img2.jpg"),
        require("@/assets/imeg/img3.jpg"),
        require("@/assets/imeg/img4.jpg"),
        require("@/assets/imeg/img5.jpg"),
        require("@/assets/imeg/img6.jpg"),

        require("@/assets/imeg/img7.jpg"),
        require("@/assets/imeg/img8.jpg"),
        require("@/assets/imeg/img7.jpg"),
        require("@/assets/imeg/img8.jpg"),
        require("@/assets/imeg/img9.jpg"),
        require("@/assets/imeg/img9.jpg"),

      ];
      const shuffledImages = images.sort(() => Math.random() - 0.5);
      this.cards.forEach((card, index) => {
        card.image = shuffledImages[index];
        card.flipped = false;
        card.clickable = true;
        card.shake = false;
      });
      this.disableDeck = this.isPlaying = this.isWinner = false;
    },
    startNewGame() {
      this.shuffleCard();
      this.maxTime = 250;
      this.timeLeft = 250;
      this.timer = setInterval(this.initTimer, 1000);
      this.isPlaying = true;
      this.shuffleCard();
      this.totalWins = 0;
      this.resetGame();
    },
    nextLevel() {
      this.isWinner = false;
      this.showNextLevelButton = true;
      console.log("Next Level function called");
      this.maxTime -= 30;
      this.totalWins++;
      if (this.totalWins === 6) {
        alert("Congratulations! You've won 6 times!");
        this.totalWins = 0;
        this.shuffleCard();
        this.maxTime = 250;
        this.timeLeft = 250;
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

      this.disableDeck =
        this.isPlaying =
        this.isWinner =
        this.showNextLevelButton =
          false;
    },
    mounted() {
      this.shuffleCard();
    },
    watch: {
      isWinner(newValue) {
        if (newValue) {
          console.log("Next Level triggered");
          this.showNextLevelButton = true;

          this.nextLevelLogic();
        }
      },
      nextLevelLogic() {
        console.log("Next Level logic executed");
      },
    },
  },
};
</script>
