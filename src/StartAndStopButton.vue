<template>
  <button id="start-and-stop" @click="() => { this.$emit('pressed') }" :class="{ red: (started && !stopped) }">
    {{ (started && !stopped) ? 'Stop' : 'Start' }}
  </button>
</template>

<script>
export default {
  name: 'StartAndStopButton',
  props: ['started', 'stopped']
}
</script>

<style>
button#start-and-stop {
  width: 80px;
  height: 80px;
  outline: none;
  background-color: #333;
  border: 2px solid #000;
  border-radius: 100%;
  color: #fff;
  font-size: 1rem;
  box-shadow: 0px 0px 0px 2px #333;
  float: left;
}

button#start-and-stop {
  background-color: #082A12;
  box-shadow: 0px 0px 0px 2px #082A12;
  color: #2ED158;
  float: right;
}

button#start-and-stop.red {
  background-color: #320E0B;
  box-shadow: 0px 0px 0px 2px #320E0B;
  color: #FF453A;
}
</style>