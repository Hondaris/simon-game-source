<template>
  <div id="app">
    <div class="wrap_game">
      <div></div>
      <div>
        <div class="label_round">Round</div>
        <div class="round">{{ round }}</div>
        <div class="panel_circle">
          <div class="circle"></div>
          <div class="panel_wrap">
            <div @click="clickPanel" class="panel panel_blue"></div>
            <div @click="clickPanel" class="panel panel_red"></div>
          </div>
          <div class="panel_wrap">
            <div @click="clickPanel" class="panel panel_green"></div>
            <div @click="clickPanel" class="panel panel_yellow"></div>
          </div>
        </div>
        <audio id="sound_blue" src="./assets/blue-sound.mp3"></audio>
        <audio id="sound_green" src="./assets/green-sound.mp3"></audio>
        <audio id="sound_red" src="./assets/red-sound.mp3"></audio>
        <audio id="sound_yellow" src="./assets/yellow-sound.mp3"></audio>
      </div>
      <div>
        <div class="record">Your record: {{ record }}</div>
        <input
          v-model="difficulty"
          type="radio"
          name="difficulty"
          value="1500"
        /><label class="name_difficulty" for="difficulty">Easy</label>
        <br />
        <input
          v-model="difficulty"
          type="radio"
          name="difficulty"
          value="1000"
        /><label class="name_difficulty" for="difficulty">Middle</label>
        <br />
        <input
          v-model="difficulty"
          type="radio"
          name="difficulty"
          value="400"
        /><label class="name_difficulty" for="difficulty">Hard</label>
        <br />
        <button class="start_game" @click="startGame">Start New Game</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      round: 0,
      record: 0,
      canClick: false,
      sequenceToGuess: [],
      sequence: [],
      difficulty: '1500',
    }
  },
  methods: {
    async click(el) {
      function flash(el) {
        return new Promise((resolve) => {
          el.classList.add('click_active')
          if (el.classList[1] === 'panel_red') {
            document.getElementById('sound_red').play()
          } else if (el.classList[1] === 'panel_blue') {
            document.getElementById('sound_blue').play()
          } else if (el.classList[1] === 'panel_green') {
            document.getElementById('sound_green').play()
          } else if (el.classList[1] === 'panel_yellow') {
            document.getElementById('sound_yellow').play()
          }
          setTimeout(() => {
            el.classList.remove('click_active')
            resolve()
          }, 100)
        })
      }
      await flash(el)
    },
    async clickPanel(e) {
      if (!this.canClick) {
        return
      }
      this.click(e.target)
      const exceptedPanel = this.sequenceToGuess.shift()
      if (exceptedPanel === e.target) {
        if (this.sequenceToGuess.length === 0) {
          this.sequence.push(this.getRandomPanel())
          this.sequenceToGuess = [...this.sequence]
          this.round++
          setTimeout(() => this.startFlashing(this.sequence), 1000)
        }
      } else {
        if (this.round > this.record) {
          this.record = this.round
        }
        this.round = 'Game Over'
        this.canClick = false
      }
    },
    async startFlashing(sequence) {
      const delay = Number(this.difficulty)
      this.canClick = false
      function flash(panel) {
        return new Promise((resolve) => {
          panel.classList.add('active')
          if (panel.classList[1] === 'panel_red') {
            document.getElementById('sound_red').play()
          } else if (panel.classList[1] === 'panel_blue') {
            document.getElementById('sound_blue').play()
          } else if (panel.classList[1] === 'panel_green') {
            document.getElementById('sound_green').play()
          } else if (panel.classList[1] === 'panel_yellow') {
            document.getElementById('sound_yellow').play()
          }
          setTimeout(() => {
            panel.classList.remove('active')
            setTimeout(() => {
              resolve()
            }, delay)
          }, 200)
        })
      }

      for (const panel of sequence) {
        await flash(panel)
      }

      this.canClick = true
    },
    getRandomPanel() {
      const panels = [
        document.querySelector('.panel_blue'),
        document.querySelector('.panel_red'),
        document.querySelector('.panel_green'),
        document.querySelector('.panel_yellow'),
      ]
      return panels[parseInt(panels.length * Math.random())]
    },
    async startGame() {
      this.round = 0

      this.sequence = [this.getRandomPanel()]

      this.sequenceToGuess = [...this.sequence]

      this.startFlashing(this.sequence)
    },
  },
}
</script>

<style>
#app {
  background-color: #000;
  padding-bottom: 250px;
}

.round {
  color: #fff;
  text-align: center;
  font-size: 50px;
}

.label_round {
  color: #fff;
  text-align: center;
  font-size: 50px;
}

.panel_circle {
  position: relative;
}

.circle {
  background-color: #000;
  width: 100px;
  height: 100px;
  position: absolute;
  left: 38%;
  bottom: 38%;
  border-radius: 100%;
  z-index: 5;
}

.wrap_game {
  display: flex;
  width: 100%;
  justify-content: space-around;
  padding-top: 50px;
  align-items: center;
}

.panel {
  width: 200px;
  height: 200px;
  margin: 5px;
}

.panel_blue {
  background-color: #0000ff;
  border-top-left-radius: 100%;
}

.panel_red {
  background-color: red;
  border-top-right-radius: 100%;
}

.panel_green {
  background-color: green;
  border-bottom-left-radius: 100%;
}

.panel_yellow {
  background-color: yellow;
  border-bottom-right-radius: 100%;
}

.panel_wrap {
  display: flex;
}

.start_game {
  width: 150px;
  height: 50px;
  text-decoration: none;
  color: #a675b3;
  text-align: center;
  line-height: 20px;
  display: block;
  margin: 20px auto;
  font: normal 17px arial;
}

.start_again {
  width: 150px;
  height: 50px;
  text-decoration: none;
  color: #fff;
  background: red;
  text-align: center;
  line-height: 20px;
  display: none;
  margin: 20px auto;
  font: normal 17px arial;
}

.button_clicked {
  display: none;
}

.name_difficulty {
  color: #fff;
  margin-bottom: 15px;
  font-size: 20px;
  display: inline-block;
}

.click_active {
  background-color: #fff;
}

.record {
  color: #fff;
  font-size: 30px;
  margin-bottom: 20px;
}

.active {
  transform: scale(1.1);
  background-color: #fff;
}
</style>
