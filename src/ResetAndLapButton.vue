<template>
  <button id="reset-and-lap" @click="() => { this.$emit('pressed') }" :class="{ red: (started && !stopped) }">
    {{ (started && !stopped) ? 'Lap' : 'Reset' }}
  </button>
</template>

<script>
export default {
  name: 'StartAndStopButton',
  props: ['started', 'stopped']
}
</script>

<style>
button#reset-and-lap {
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
</style>