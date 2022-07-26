<template>
  <div
    class="wrapper cursor-pointer"
    :class="{ 'opacity-50': disabled }"
  >
    <div ref="dice" :class="diceClass">
      <div class="side one">
        <div class="dot one-1" />
      </div>
      <div id="dice-one-side-two" class="side two">
        <div class="dot two-1" />
        <div class="dot two-2" />
      </div>
      <div id="dice-one-side-three" class="side three">
        <div class="dot three-1" />
        <div class="dot three-2" />
        <div class="dot three-3" />
      </div>
      <div id="dice-one-side-four" class="side four">
        <div class="dot four-1" />
        <div class="dot four-2" />
        <div class="dot four-3" />
        <div class="dot four-4" />
      </div>
      <div id="dice-one-side-five" class="side five">
        <div class="dot five-1" />
        <div class="dot five-2" />
        <div class="dot five-3" />
        <div class="dot five-4" />
        <div class="dot five-5" />
      </div>
      <div id="dice-one-side-six" class="side six">
        <div class="dot six-1" />
        <div class="dot six-2" />
        <div class="dot six-3" />
        <div class="dot six-4" />
        <div class="dot six-5" />
        <div class="dot six-6" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameDice',

  props: {
    value: {
      type: Number,
      default: 1
    },

    disabled: {
      type: Boolean,
      default: false
    },

    soundEnabled: {
      type: Boolean,
      default: false
    },

    color: {
      type: String,
      default: 'black',
      validator: value => ['black', 'red', 'green', 'blue', 'yellow', 'orange'].includes(value)
    }
  },

  data () {
    return {
      previousValue: null,
      values: []
    }
  },

  computed: {
    diceClass () {
      return `dice dice--${this.color}`
    }
  },

  watch: {
    value: {
      immediate: true,
      handler (value) {
        this.$nextTick(() => {
          this.rollDice(value)
        })
      }
    }
  },

  methods: {
    rollDice (newValue) {
      this.playSound()

      const dice = this.$refs.dice

      if (dice === undefined) {
        return
      }

      for (let i = 1; i <= 6; i++) {
        dice.classList.remove('show-' + i)

        if (newValue === i) {
          dice.classList.add('show-' + i)
        }
      }
    },

    playSound () {
      if (!this.soundEnabled) {
        return
      }

      const audioFile = require('@/assets/audio/dice.mp3').default
      const audio = new Audio(audioFile)

      audio.play()
    }
  }
}
</script>

<style lang="css" scoped>
.wrapper {
  @apply inline-block relative;

  padding: 50px;
}

.dice {
  position: relative;
  width: 100px;
  height: 100px;
  transform-style: preserve-3d;
  transition: transform 1s;
}

.dot {
  position: absolute;
  width: 20px;
  height: 20px;
  margin: -10px 5px 5px -10px;
  border-radius: 20px;
}

.dice.dice--yellow .dot {
  background-color: #ffe46b;
  box-shadow: inset 2px 2px #ffd200;
}

.dice.dice--orange .dot {
  background-color: #ffc57a;
  box-shadow: inset 2px 2px #ff9100;
}

.dice.dice--red .dot {
  background-color: #f25f5c;
  box-shadow: inset 2px 2px #d90429;
}

.dice.dice--green .dot {
  background-color: #1eff8b;
  box-shadow: inset 2px 2px #00a651;
}

.dice.dice--blue .dot {
  background-color: #2f78ff;
  box-shadow: inset 2px 2px #0051e8;
}

.dice.dice--black .dot {
  background-color: #545454;
  box-shadow: inset 2px 2px #222222;
}

.side {
  position: absolute;
  background-color: #ffF;
  border-radius:5px;
  width: 100px;
  height: 100px;
  border: 1px solid #e5e5e5;
  text-align: center;
  line-height: 2em;
}

.side:nth-child(1) {
  transform: translateZ(3.1em); }

.side:nth-child(6) {
  transform: rotateY(90deg) translateZ(3.1em); }

.side:nth-child(3) {
  transform: rotateY(-90deg) translateZ(3.1em); }

.side:nth-child(4) {
  transform: rotateX(90deg) translateZ(3.1em); }

.side:nth-child(5) {
  transform: rotateX(-90deg) translateZ(3.1em); }

.side:nth-child(2) {
  transform: rotateY(-180deg) translateZ(3.1em); }

.show-1 {
  transform: rotateX(720deg) rotateZ(-720deg); }

.show-6 {
  transform: rotateY(-450deg) rotateZ(-1440deg); }

.show-3 {
  transform: rotateY(810deg) rotateZ(720deg); }

.show-4 {
  transform: rotateX(-810deg) rotateZ(-1080deg); }

.show-5 {
  transform: rotateX(450deg) rotateZ(-720deg); }

.show-2 {
  transform: rotateX(-900deg) rotateZ(1080deg); }

.two-1, .three-1, .four-1, .five-1, .six-1 {
  top: 20%;
  left: 20%;
}

.four-3, .five-3, .six-4 {
  top: 20%;
  left: 80%; }

.one-1, .three-2, .five-5 {
  top: 50%;
  left: 50%; }

.four-2, .five-2, .six-3 {
  top: 80%;
  left: 20%; }

.two-2, .three-3, .four-4, .five-4, .six-6 {
  top: 80%;
  left: 80%; }

.six-2 {
  top: 50%;
  left: 20%; }

.six-5 {
  top: 50%;
  left: 80%;
}
</style>
