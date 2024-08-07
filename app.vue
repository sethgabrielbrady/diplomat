
<script>
  import './assets/main.css';
  import DiplomatSlide from './components/DiplomatSlide.vue';
  import { ref } from 'vue';

  export default {
    components: {
      DiplomatSlide
    },
    setup() {
      let isActive = ref(false);

      function toggleActive() {
        isActive.value = !isActive.value;
      }

      return {
        isActive,
        toggleActive
      }
    }
  };
</script>

<template>
  <div class="landing-page">
    <div
      @click="toggleActive"
      class="slide-overlay"
      :class="{ 'slide-active': isActive }"
    />
    <DiplomatSlide :isActive="isActive" @closeSlide="toggleActive" />
  </div>
</template>

<style scoped>
  .landing-page {
    background-image: url('./assets/Bonvoy.png');
    background-size: cover;
    height: 100vh;
    overflow-y: hidden;
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
    pointer-events: none;
  }
</style>
