<template>
  <button
    class="border-2 border-black px-4 py-2 rounded-full"
    @click="connect"
  >
    Connect GoDice
  </button>
</template>

<script>
import GoDice from '../lib/godice'

export default {
  name: 'ConnectDice',

  props: {
    value: {
      type: Object,
      default: () => ({})
    }
  },

  data () {
    return {
      dice: {},
      connectedDice: {},
      state: 'idle',
      checkBatteryInterval: null
    }
  },

  watch: {
    state (state) {
      if (state === 'connected') {
        this.$set(this.connectedDice, this.dice.id, this.dice)

        this.dice.instance.getDiceColor()
      }

      if (state === 'color set') {
        this.dice.instance.getBatteryLevel()
      }

      if (state === 'battery level set') {
        this.dice.instance.getDiceValue()
      }

      if (state === 'dice value set') {
        this.$emit('input', this.connectedDice)

        this.dice.instance.pulseLed(5, 30, 20, [0, 0, 255])

        // reset state
        this.dice = {}
        this.state = 'idle'
      }
    }
  },

  created () {
    this.checkBatteryStatus()

    GoDice.prototype.onDiceConnected = (diceId, diceInstance) => {
      this.dice = {
        id: diceId,
        instance: diceInstance,
        color: 'black',
        batteryLevel: 0,
        rollling: false,
        value: 0,
        invalidMove: false
      }

      this.state = 'connected'
    }

    GoDice.prototype.onDiceColor = (diceId, color) => {
      const colors = this.dice.instance.diceColour
      const colorName = Object.keys(colors).find(key => colors[key] === color)

      this.updateDiceProp(diceId, 'color', colorName.toLowerCase())
      this.state = 'color set'
    }

    GoDice.prototype.onBatteryLevel = (diceId, batteryLevel) => {
      if (this.state === 'color set') {
        this.state = 'battery level set'
      }

      this.updateDiceProp(diceId, 'batteryLevel', batteryLevel)
    }

    GoDice.prototype.onRollStart = (diceId) => {
      this.updateDiceProp(diceId, 'invalidMove', false)
      this.updateDiceProp(diceId, 'rollling', true)
    }

    GoDice.prototype.onStable = (diceId, value, xyzArray) => {
      if (this.state === 'battery level set') {
        this.state = 'dice value set'
      }

      this.updateDiceProp(diceId, 'invalidMove', false)
      this.updateDiceProp(diceId, 'rollling', false)
      this.updateDiceProp(diceId, 'value', parseInt(value))
    }

    GoDice.prototype.onMoveStable = (diceId, value) => {
      this.updateDiceProp(diceId, 'invalidMove', true)
      this.updateDiceProp(diceId, 'rollling', false)
      this.updateDiceProp(diceId, 'value', parseInt(value))
    }

    GoDice.prototype.onFakeStable = (diceId, value) => {
      this.updateDiceProp(diceId, 'invalidMove', true)
      this.updateDiceProp(diceId, 'rollling', false)
      this.updateDiceProp(diceId, 'value', parseInt(value))
    }
  },

  methods: {
    connect () {
      const newDice = new GoDice()

      this.state = 'connecting'
      newDice.requestDevice()
    },

    checkBatteryStatus () {
      if (Object.keys(this.connectedDice) === 0) {
        clearInterval(this.checkBatteryInterval)
        return
      }

      this.checkBatteryInterval = setInterval(() => {
        Object.keys(this.connectedDice).forEach((diceId) => {
          this.connectedDice[diceId].instance.getBatteryLevel()
        })
      }, 60000)
    },

    updateDiceProp (diceId, prop, value) {
      this.$set(this.connectedDice[diceId], prop, value)
    }
  }
}
</script>
