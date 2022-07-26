<template>
  <div class="py-20">
    <div class="py-4 text-center">
      <connect-dice v-model="connectedDice" />
    </div>
    <div class="flex items-center justify-center">
      <div
        v-for="(dice, diceId) in connectedDice"
        :key="diceId"
        class="flex flex-col items-center justify-center"
      >
        <game-dice
          :value="dice.value"
          :color="dice.color"
        />
        <div class="text-xs flex flex-col items-center justify-center gap-2">
          <div :class="(dice.batteryLevel < 10) ? 'text-red-500' : 'text-gray-500'">
            {{ dice.batteryLevel }}%
          </div>
          <div>{{ dice.rollling ? 'würfelt ...' : '' }}</div>
          <div>{{ dice.invalidMove ? 'Wurf ungültig, nochmal!' : '' }}</div>
        </div>
      </div>

      <div
        v-show="Object.keys(connectedDice).length === 0"
        class="py-20 opacity-50"
      >
        Noch keine Würfel verbunden.
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',

  data () {
    return {
      connectedDice: {}
    }
  }
}
</script>
