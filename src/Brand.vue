<template>
  <span id="brand">
    <slot />
  </span>
</template>

<script>
export default {
  name: 'Brand',
}
</script>

<style scoped>
#brand {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translateX(-50%) translateY(-50%);
}
</style>