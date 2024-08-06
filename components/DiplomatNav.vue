<script>
  export default defineComponent({
    setup() {
      return {
        navItems: ['CURRENT TASKS', 'NEW REQUEST', 'MESSAGE CONCIERGE', 'DOCUMENTATION']
      }
    },
  })
</script>

<template>
  <div class="nav-container">
    <div class="svg-arrow">
      <svg width="3em" height="3em" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M4 12H20M20 12L16 8M20 12L16 16" stroke="#FFF22F" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
      </svg>
    </div>
    <nav class="diplomat-nav">
      <div class="nav-gradient"/>
      <ul class="nav-items">
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

<style scoped lang="css">
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
    background: rgba(0, 0, 0);
    background: linear-gradient(90deg, rgba(0,0,0,0) 0%, rgba(0,0,0,0.01866684173669464) 84%, rgba(0,0,0,0.7385547969187676) 100%);
    height: 100%;
    position: absolute;
    width: 55vw;
    height: 100px;
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
    font-family: 'monotype', sans-serif;
    font-size: 1em;
    font-weight: 100;
    margin-right: 14em;
    padding-right: 0em;
    text-transform: uppercase;
    width: max-content;
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
    right: 1.4em;
    transform: translateY(-3em);
  }
</style>