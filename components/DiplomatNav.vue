<script>
  import { reactive } from 'vue';

  export default {
    name: 'DiplomatNav',
    setup(props, { emit }) {
      const navItems = reactive(["Current Tasks", "New Request", "Message Concierge", "Documentation"]);

      function updateNavItem() {
        navItems.push(navItems.shift());
        emit('activeNavItem', navItems[0]);
      }

      return {
        navItems,
        updateNavItem,
      }
    }
  }
</script>

<template>
  <div class="nav-container">
    <div class="svg-arrow" @click="updateNavItem">
      <svg width="3em" height="3em" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M4 12H20M20 12L16 8M20 12L16 16" stroke="#FFF22F" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
      </svg>
    </div>
    <div class="nav-gradient" />
    <nav class="diplomat-nav">
      <ul class="nav-items ">
        <li
          v-for="item in navItems"
          :key="item"
          class="nav-item"
        >
        {{ item }}
        </li>
      </ul>
    </nav>
  </div>
</template>

<style scoped>
  .nav-container {
    padding: 1em 0;
  }

  .diplomat-nav {
    -ms-overflow-style: none; /* for Internet Explorer, Edge */
    overflow-x: scroll;
    position: relative;
    width: 100%;
  }

  .nav-gradient {
    background: linear-gradient(90deg, rgba(0,0,0,0) 0%, rgba(0,0,0,0.5032606792717087) 50%, rgba(0,0,0,0.9814119397759104) 100%);
    height: 20px;
    position: absolute;
    right: 0;
    width: 11vw;
    z-index: 2;
  }

  .diplomat-nav::-webkit-scrollbar {
    display: none;
  }

  .nav-items {
    display: flex;
    flex-direction: row;
    justify-content: start;
    list-style-type: none;
    margin: 0;
    overflow-x: scroll;
    padding: 0;
    position: relative;
    width: max-content;
  }

  .nav-item {
    border-bottom: 2px solid #fff12f00;
    cursor: pointer;
    display: inline;
    font-family: sans-serif;
    font-size: 1.025em;
    font-weight: 100;
    margin-right: 8.5em;
    padding-right: 0em;
    text-transform: uppercase;
    width: max-content;
  }

  .nav-item:first-child {
    border-bottom: 2px solid #FFF22F;
  }

  .nav-item:hover{
    border-bottom: 2px solid #FFF22F;
  }

  .svg {
    width: max-content;
  }

  .svg-arrow {
    cursor: pointer;
    position: absolute;
    right: 3em;
    transform: translateY(-3em);
  }
</style>