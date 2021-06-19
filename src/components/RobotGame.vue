<template>
  <div id="game">
    <div v-show="gameOver == ''">
      <div id="top-bar">
        <div class="r-row">
          <div class="r-col">
            <Score :score="score" />
          </div>
          <div class="r-col pull-right">
            <Timer />
          </div>
        </div>
      </div>

      <div class="r-row" v-for="x in 5" :key="x">
        <div class="r-col" v-for="y in 5" :key="y">
          <div :class="generateClassName(x, y)">
            <div v-if="isRobotHere(x, y)">
              <RobotIcon
                :style="{ transform: 'rotate(' + robotDirection + 'deg)' }"
              />
            </div>
            <div v-if="isFlagHere(x, y)"><FlagIcon /></div>
          </div>
        </div>
      </div>

      <div class="r-row">
        <p align="center">
          Move the robot around the board by rotating and moving it and capture
          the flag as fast as you can. The timer starts when you make your first
          move!
        </p>
        <p align="center">How many flags you can capture in 60 seconds?</p>
      </div>

      <div class="r-row">
        <div class="r-container-centered">
          <button @click="rotateLeft">
            <LeftTurnIcon />
          </button>
          <button @click="moveForward">
            <UpIcon />
          </button>
          <button @click="rotateRight">
            <RightTurnIcon />
          </button>
        </div>
      </div>
    </div>
    <div v-show="gameOver !== ''" class="r-game-over">
      <div class="title">Game Over 😭</div>
      <div class="subtitle">
        <em>{{ gameOver }}</em>
        <br />Final score: {{ score }}
      </div>
      <button class="r-reset" @click="reset"><LeftTurnIcon /></button>
    </div>
  </div>
</template>


<script>
import Score from './Score.vue'
import Timer from './Timer.vue'
import RobotIcon from './icons/Robot.vue'
import FlagIcon from './icons/Flag.vue'

import LeftTurnIcon from './icons/LeftTurn.vue'
import RightTurnIcon from './icons/RightTurn.vue'
import UpIcon from './icons/Up.vue'

import eventHub from './events'

const direction = {
  Left: 270,
  Top: 0,
  Right: 90,
  Bottom: 180
}

const defaultValues = {
  robot: { x: 3, y: 3, rotation: direction.Top },
  minPos: 1,
  maxPos: 5,
  timer: 60
}

export default {
  components: {
    Score,
    Timer,
    RobotIcon,
    FlagIcon,
    LeftTurnIcon,
    RightTurnIcon,
    UpIcon
  },

  data () {
    return {
      robotState: { x: 0, y: 0, rotation: direction.Top },
      flagPosition: { x: 0, y: 0 },
      gameOver: '',
      moveCount: false,
      score: 0
    }
  },

  computed: {
    robotDirection () {
      return this.robotState.rotation;
    },

    timer () {
      return defaultValues.timer;
    }
  },

  watch: {
    moveCount () {
      if (this.moveCount === 1) eventHub.$emit('start-timer');
    }
  },

  methods: {
    generateClassName (x, y) {
      return `r-cell cell-${x}-${y}`
    },

    isRobotHere (x, y) {
      return x == this.robotState.x && y == this.robotState.y
    },

    isFlagHere (x, y) {
      return x == this.flagPosition.x && y == this.flagPosition.y
    },

    rotateLeft () {
      this.moveCount++;

      switch (this.robotState.rotation) {
        case direction.Left:
          this.robotState.rotation = direction.Bottom
          break;

        case direction.Top:
          this.robotState.rotation = direction.Left
          break;

        case direction.Right:
          this.robotState.rotation = direction.Top
          break;

        case direction.Bottom:
          this.robotState.rotation = direction.Right
          break;
      }
    },

    rotateRight () {
      this.moveCount++;

      switch (this.robotState.rotation) {
        case direction.Left:
          this.robotState.rotation = direction.Top
          break;

        case direction.Top:
          this.robotState.rotation = direction.Right
          break;

        case direction.Right:
          this.robotState.rotation = direction.Bottom
          break;

        case direction.Bottom:
          this.robotState.rotation = direction.Left
          break;
      }
    },

    moveForward () {
      this.moveCount++;

      switch (this.robotState.rotation) {
        case direction.Left:
          this.robotState.y--
          break;
        case direction.Top:
          this.robotState.x--
          break;
        case direction.Right:
          this.robotState.y++
          break;
        case direction.Bottom:
          this.robotState.x++
          break;
      }

      if (this.robotState.x < defaultValues.minPos || this.robotState.x > defaultValues.maxPos ||
        this.robotState.y < defaultValues.minPos || this.robotState.y > defaultValues.maxPos) {
        this.gameOver = 'You ran the robot off the grid, you mean!'
      }

      if (this.robotState.x === this.flagPosition.x && this.robotState.y === this.flagPosition.y) {
        this.score++;
        this.randomizeFlagLocation();
      }
    },

    rndExclude (min, max, exclude) {
      let n = Math.floor(Math.random() * (max - min) + min);
      if (n >= exclude) n++;
      return n
    },

    randomizeFlagLocation () {
      this.flagPosition = {
        x: this.rndExclude(defaultValues.minPos, defaultValues.maxPos, this.robotState.x),
        y: this.rndExclude(defaultValues.minPos, defaultValues.maxPos, this.robotState.y)
      }
    },

    reset () {
      this.gameOver = '';
      this.moveCount = 0;
      this.robotState = { ...defaultValues.robot };
      this.randomizeFlagLocation();
      eventHub.$emit('set-timer', defaultValues.timer);
    }
  },

  mounted () {
    this.$nextTick(() => this.reset());

    eventHub.$on('timer-zero', () => {
      this.gameOver = 'The time ran up for your robotic friend!';
    });
  },

  beforeDestroy () {
    eventHub.$off('timer-zero');
  }
}
</script>


<style>
* {
  font-family: Arial, Helvetica, sans-serif;
}

#game {
  display: block;
  margin: 0 auto;
  width: 420px;
}

#top-bar {
  margin-bottom: 15px;
}

.r-row {
  display: block;
  width: 100%;
  margin: 0;
  box-sizing: border-box;
}

.r-row::after {
  content: "";
  clear: both;
  display: table;
}

.r-col.pull-right {
  text-align: right;
  float: right;
}

.r-col {
  display: block;
  float: left;
  margin: 0;
  box-sizing: border-box;
}

.r-cell {
  width: 80px;
  height: 80px;
  background: #eee;
  margin: 0 5px 5px 0;
}

.r-col:nth-child(5) .r-cell {
  margin: 0;
}

p {
  line-height: 1.3em;
}

.r-container-centered {
  display: block;
  text-align: center;
  margin-top: 15px;
}

.r-container-centered button:nth-child(2) {
  margin: 0 10px;
}

.r-game-over {
  text-align: center;
}

.r-game-over .title {
  font-size: 26px;
  font-weight: bold;
  margin-top: 40px;
}

.r-game-over .subtitle {
  padding-top: 12px;
  padding-bottom: 30px;
  font-size: 16px;
  line-height: 1.5em;
}

.r-game-over .r-reset {
  padding: 3px 5px;
}
</style>
