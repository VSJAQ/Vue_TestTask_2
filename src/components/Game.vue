<template name="Vue-Simon-The-Game">
  <div class="game">
    <div class="gameBoard">
      <div class="allButton">
        <div
          class="buttonGreen"
          :class="{ 'active': activeButton === 1 && gameInProcess}"
          v-on:click="clickButton(1)"
        ></div>
        <div
          class="buttonBlue"
          :class="{ 'active': activeButton == 2 && gameInProcess}"
          v-on:click="clickButton(2)"
        ></div>
        <div
          class="buttonYellow"
          :class="{ 'active': activeButton === 3 && gameInProcess}"
          v-on:click="clickButton(3)"
        ></div>
        <div
          class="buttonRed"
          :class="{ 'active': activeButton === 4 && gameInProcess}"
          v-on:click="clickButton(4)"
        ></div>
      </div>
      <div class="gameControl">
        <button class="startButton" :disabled="gameInProcess" v-on:click="playGame()">Начать</button>
        <p class="gameMsg" v-if="fail === false">Раунд {{round}}</p>
        <p class="successGameMsg" v-if="success === true">Уровень пройден!</p>
        <p class="endGameMsg" v-if="fail === true">
          Ты ошибся!
          <br />Твой счёт уровней: {{round}}!
          <br />Попробуй ещё раз!
        </p>
        <p class="gameMsg" v-if="condition === 'ready'">Готовься...</p>
        <p class="gameMsg" v-if="condition === 'remember'">Запоминай...</p>
        <p class="gameMsg" v-if="condition === 'go'">Нажимай!</p>
      </div>
    </div>
  </div>
</template>

<script>
import "./Game.css"
export default {
  name: "Game",

  data: () => ({
    gameSequence: [],
    userSequence: [],
    activeButton: null,
    fail: false,
    round: 1,
    gameInProcess: false,
    success: false,
    buttonTimer: null,
    showerTimer: null,
    buttonEnabled: false,
    condition: false,
    conditionTimeout: null,
  }),

  methods: {
    playGame() {
      this.gameInProcess = true
      this.gameSequence = []
      this.userSequence = []
      this.round = 1
      this.fail = false
      this.generateSeries()
      this.showSeries()
    },

    generateSeries() {
      this.gameSequence.push(Math.floor(Math.random() * 4 + 1))
    },

    showSeries() {
      let i = 0
      clearTimeout(this.conditionTimeout)
      this.condition = 'ready'
      
      setTimeout(() => {
        setTimeout(() => {
          this.condition = 'remember'
          }, 1000)
        let interval = setInterval(() => {
          this.activeButton = this.gameSequence[i]
          this.playSound(this.gameSequence[i])
          clearTimeout(this.buttonTimer)
          this.buttonTimer = setTimeout(() => {
            this.activeButton = null
          }, 1000)
          i++;
          if (i > this.gameSequence.length) {
            clearInterval(interval)
            this.condition = 'go'
            this.conditionTimeout = setTimeout(() => {
            this.condition = null
            }, 2000)
            this.buttonEnabled = true
          }
        }, 1200);
      }, 500);
    },

    clickButton(e) {
      if (this.gameInProcess && this.buttonEnabled) {
        this.success = false
        this.activeButton = e;
        this.playSound(e)
        clearTimeout(this.buttonTimer)
        this.buttonTimer = setTimeout(() => {
          this.activeButton = null
        }, 1000)

        let index = this.userSequence.length

        if (this.gameSequence[index] === e) {
          this.userSequence.push(e)
        } else {
          this.fail = true
          this.gameStarted = false
        }

        if (
          this.gameSequence.length === this.userSequence.length &&
          this.fail == false
        ) {
          this.success = true
          this.nextRound()
        } else if (this.fail == true) {
          this.success = false
          this.gameInProcess = false
          this.condition = null
        } else {
          return
        }
      }
    },

    playSound (e) {
      if (0 < e < 5 && e !== undefined) {
      let audio = new Audio(require('../../sounds/' + e + '.mp3'))
      audio.play()
      } else{
        return
      }
		},

    nextRound() {
      this.buttonEnabled = false
      this.round++
      this.userSequence = []
      this.generateSeries()
      this.showSeries()
    }
  }
}
</script>