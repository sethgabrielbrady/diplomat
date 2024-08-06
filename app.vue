
<script>
  export default {
    setup() {
      return {
        isActive: ref(false),
      }
    },
    methods: {
      toggleActive() {
        this.isActive = !this.isActive;
      }
    }
  };
</script>

<template>
  <div
    @click="toggleActive()"
    class="landing-page"
  >
    <div
      class="slide-overlay"
      :class="{ 'slide-active': isActive }"
    />
    <DiplomatSlide :isActive="isActive" />
  </div>
</template>

<style scoped>
  .landing-page {
    background-image: url('./assets/Bonvoy.png');
    background-size: cover;
    height: 100vh;
    overflow: hidden;
    padding: 0;
    position: relative;
    transition: background-color 0.5s;
    width: 100vw;
  }
  .slide-overlay {
    position: absolute;
    width: 100vw;
    height: 100vh;
    background-color: #f20a0a00;
    transition: background-color 0.5s;
  }
  .slide-active {
    background-color: #000000c4;
  }
</style>
