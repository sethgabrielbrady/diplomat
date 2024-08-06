
<script setup>
  const props = defineProps({
    isActive: Boolean
  });
</script>

<template>
  <div
    class="diplomat-slide"
    :class="{ 'slide-out': !props.isActive, 'slide-in': props.isActive }"
  >
    <h1>Diplomat</h1>
  </div>
</template>

<style scoped>
  .diplomat-slide {
    background-color: #000000;
    color: aqua;
    height: 100vh;
    padding: 3em;
    position: absolute;
    top: 0;
    transition: right 0.5s;
    width: 55vw;
  }
  .slide-out {
    right: calc(-55vw - 6em);
  }
  .slide-in {
    right: 0;
  }
</style>
