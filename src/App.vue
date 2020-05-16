<template>
  <div id="app">
    <timer :time="displayTime" />

    <div id="stopwatch-controls">
      <reset-and-lap-button
        @pressed="resetAndLapHandler"
        :started="isStarted"
        :stopped="isStopped"
      />
      
      <start-and-stop-button
        @pressed="startAndStopHandler"
        :started="isStarted"
        :stopped="isStopped"
      />

      <brand>My Awesome Timer</brand>
    </div>
    <ul id="stopwatch-records">
      <li :class="{ red: lap.isSlowest, green: lap.isFastest }" v-for="(lap, index) in lapsRecords" :key="index">
        <span>Lap {{ lapsRecords.length - index }}</span>
        <span>{{ lap.display }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
import Timer from './Timer.vue'
import Brand from './Brand.vue'
import StartAndStopButton from './StartAndStopButton.vue'
import ResetAndLapButton from './ResetAndLapButton.vue'

export default {
  name: 'App',
  components: {
    Timer,
    Brand,
    StartAndStopButton,
    ResetAndLapButton,
  },
  data() {
    return {
      displayTime: '00:00.00',
      timer: null,
      startTime: null,
      stopTime: null,
      stoppedTimeOffset: 0,
      stoppedTimeOffsetForLap: 0,
      currentTime: null,
      isStarted: false,
      isStopped: false,
      laps: [],
      lastLapTime: null,
    }
  },
  watch: {
    displayTime() {
      let lapsClone = [...this.laps]

      let elapsedTime = this.currentTime - this.lastLapTime - this.stoppedTimeOffsetForLap
      let lapTime = (elapsedTime / 1000).toFixed(2)

      lapsClone[this.laps.length - 1] = {
        time: parseFloat(lapTime),
        display: this.formatTime(lapTime),
        isFastest: false,
        isSlowest: false,
      }

      this.laps = lapsClone
    }
  },
  computed: {
    lapsRecords() {
      let lapsClone = [...this.laps]

      if (lapsClone.length > 2) {
        lapsClone = lapsClone.map((oneLap) => {
          return {
            ...oneLap,
            isFastest: false,
            isSlowest: false,
          }
        })

        let fastestIndex = 0
        let slowestIndex = 0

        for (let i = 0; i < lapsClone.length; i++) {
          if (lapsClone[i].time < lapsClone[fastestIndex].time) {
            fastestIndex = i
          }

          if (lapsClone[i].time > lapsClone[slowestIndex].time) {
            slowestIndex = i
          }
        }

        lapsClone[fastestIndex].isFastest = true
        lapsClone[slowestIndex].isSlowest = true
      }

      return lapsClone.reverse()
    }
  },
  methods: {
    startAndStopHandler() {

      if (!this.isStarted) {
        this.startTime = Date.now()
        this.lastLapTime = Date.now()

        this.timer = setInterval(() => {
          this.currentTime = Date.now()
          let elapsedTime = this.currentTime - this.startTime

          this.displayTime = this.formatTime((elapsedTime / 1000).toFixed(2))
        }, 10)

        this.laps = [{
          time: 0,
          display: this.displayTime,
          isFastest: false,
          isSlowest: false,
        }]

        this.isStarted = true
        this.isStopped = false
      } else if (this.isStarted && !this.isStopped) {
        clearInterval(this.timer)
        this.stopTime = Date.now()
        this.isStopped = true
      } else if (this.isStarted && this.isStopped) {
        this.stoppedTimeOffset += Date.now() - this.stopTime
        this.stoppedTimeOffsetForLap += Date.now() - this.stopTime
        
        this.timer = setInterval(() => {
          this.currentTime = Date.now()
          let elapsedTime = this.currentTime - this.startTime - this.stoppedTimeOffset

          this.displayTime = this.formatTime((elapsedTime / 1000).toFixed(2))
        }, 10)

        this.isStopped = false
      }
    },
    resetAndLapHandler() {
      if (this.isStarted && this.isStopped) {
        this.displayTime = '00:00.0'
        this.timer = null
        this.startTime = null
        this.stopTime = null
        this.stoppedTimeOffset = 0
        this.currentTime = null
        this.isStarted = false
        this.isStopped = false

        this.stoppedTimeOffsetForLap = 0
        this.laps = []
        this.lastLapTime = null

      } else if (this.isStarted && !this.isStopped) {
        let elapsedTime = this.currentTime - this.lastLapTime - this.stoppedTimeOffsetForLap
        let lapTime = (elapsedTime / 1000).toFixed(2)

        this.laps.push({
          time: parseFloat(lapTime),
          display: this.formatTime(lapTime)
        })

        this.lastLapTime = this.currentTime
        this.stoppedTimeOffsetForLap = 0
      }
    },
    formatTime(seconds) {
      let date = new Date(null)
      date.setSeconds(seconds)
      let result = date.toISOString().substr(14, 5)

      return `${result}.${(seconds + '').split('.')[1]}`
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: #000;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  color: #fff;
}

#stopwatch-records {
  flex: 0px 1 1;
}

#stopwatch-records {
  list-style: none;
  padding: 0;
  margin: 0 1.2rem;
  overflow: auto;
}

#stopwatch-records li {
  border-bottom: 1px solid #323234;
  display: flex;
  padding: .8rem 0;
}

#stopwatch-records li.green {
  color: #2ED158;
}

#stopwatch-records li.red {
  color: #FF453A;
}

#stopwatch-records li:first-child {
  border-top: 1px solid #323234;
}

#stopwatch-records span {
  display: block;
  flex: 0px 1 1;
}

#stopwatch-records span:last-child {
  text-align: right;
}

#stopwatch-controls {
  flex: 80px 0 0;
  padding: .2rem 1.2rem;
  overflow: hidden;
  position: relative;
  margin-bottom: 1.5rem;
}
</style>
