<template>
  <div id="app">
    <div class="countdown" :style="{ '--color': color }" v-if="goal !== 0">
      <div class="element">
        <span class="number">{{ `${timeLeft.days}`.padStart(2, "0") }}</span>
        <span class="unit">d</span>
      </div>
      <div class="element">
        <span class="number">{{ `${timeLeft.hours}`.padStart(2, "0") }}</span>
        <span class="unit">h</span>
      </div>
      <div class="element">
        <span class="number">{{ `${timeLeft.minutes}`.padStart(2, "0") }}</span>
        <span class="unit">m</span>
      </div>
      <div class="element">
        <span class="number">{{ `${timeLeft.seconds}`.padStart(2, "0") }}</span>
        <span class="unit">s</span>
      </div>
    </div>
  </div>
</template>

<script>
import {
  differenceInDays,
  differenceInHours,
  differenceInMinutes,
  differenceInSeconds
} from "date-fns"

const TIMESTAMP_UPDATE_INTERVAL = 1 * 1000 // in ms

export default {
  data() {
    return {
      goal: 0,
      currentTimestamp: 0
    }
  },
  mounted() {
    const urlParams = new URLSearchParams(location.search)
    this.goal = Number(urlParams.get("goal")) * 1000
    this.updateCurrentTimestamp()
    setInterval(() => {
      this.updateCurrentTimestamp()
    }, TIMESTAMP_UPDATE_INTERVAL)
  },
  methods: {
    updateCurrentTimestamp() {
      this.currentTimestamp = Date.now()
    }
  },
  computed: {
    timeLeft() {
      const days = differenceInDays(this.goal, this.currentTimestamp)
      const hours =
        differenceInHours(this.goal, this.currentTimestamp) - days * 24
      const minutes =
        differenceInMinutes(this.goal, this.currentTimestamp) -
        days * 24 * 60 -
        hours * 60
      const seconds =
        differenceInSeconds(this.goal, this.currentTimestamp) -
        days * 24 * 60 * 60 -
        hours * 60 * 60 -
        minutes * 60
      return {
        days,
        hours,
        minutes,
        seconds
      }
    },
    color() {
      const hours = Math.floor((this.timeLeft.days / 24) * 255).toString(16)
      const minutes = Math.floor((this.timeLeft.minutes / 60) * 255).toString(
        16
      )
      const seconds = Math.floor((this.timeLeft.seconds / 60) * 255).toString(
        16
      )

      return `#${hours}${minutes}${seconds}`
    }
  }
}
</script>

<style lang="sass">
body
  margin: 0

#app
  font-family: 'IBM Plex Sans', Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  color: white

  .countdown
    width: 100vw
    height: 100vh
    background-color: var(--color, black)
    display: flex
    align-items: center
    justify-content: center
    gap: 3vw
    transition: background-color 1s linear

    .element
      .number
        font-size: 9vw

      .unit
        font-size: 4vw
</style>
