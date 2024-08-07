<script>
  import { ref, reactive } from 'vue';
  import DiplomatNav from './DiplomatNav.vue';
  import CurrentTasks from './CurrentTasks.vue';
  import MessageConcierge from './MessageConcierge.vue';
  import Documentation from './Documentation.vue';
  import NewRequest from './NewRequest.vue';

  export default {
    components: {
      DiplomatNav,
      CurrentTasks,
      MessageConcierge,
      Documentation,
      NewRequest,
    },
    name: 'DiplomatSlide',
    props: {
      isActive: {
        type: Boolean,
        required: true
      }
    },
    setup(props, { emit }) {
      let activeComponent = ref('CurrentTasks');

      const emitCloseSlide = () => {
        emit('closeSlide');
      };

      const handleActiveNavItem = (item) => {
        activeComponent.value = item.replace(/ /g,'');
      };

      return {
        props,
        emitCloseSlide,
        handleActiveNavItem,
        activeComponent
      }
    }
  }
</script>

<template>
  <div
    class="diplomat-slide"
    :class="{ 'slide-out': !props.isActive, 'slide-in': props.isActive }"
  >
    <div class="user-icon" />
    <div class="svg-close" @click="emitCloseSlide">
      <svg width="100%" height="100%" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" style="pointer-events: none;">
        <path  d="M6.99486 7.00636C6.60433 7.39689 6.60433 8.03005 6.99486 8.42058L10.58 12.0057L6.99486 15.5909C6.60433 15.9814 6.60433 16.6146 6.99486 17.0051C7.38538 17.3956 8.01855 17.3956 8.40907 17.0051L11.9942 13.4199L15.5794 17.0051C15.9699 17.3956 16.6031 17.3956 16.9936 17.0051C17.3841 16.6146 17.3841 15.9814 16.9936 15.5909L13.4084 12.0057L16.9936 8.42059C17.3841 8.03007 17.3841 7.3969 16.9936 7.00638C16.603 6.61585 15.9699 6.61585 15.5794 7.00638L11.9942 10.5915L8.40907 7.00636C8.01855 6.61584 7.38538 6.61584 6.99486 7.00636Z" fill="#FFFFFF"/>
      </svg>
    </div>
    <p class="slide-title">
      <span class="title-small">THE</span>
      <span class="title-large">DIPLOMAT</span>
    </p>
    <DiplomatNav @activeNavItem="handleActiveNavItem" />
    <div class="nav-item-container" >
      <component :is="activeComponent" />
    </div>
  </div>
</template>

<style scoped="scss">
  .diplomat-slide {
    background-color: #000000;
    color:#fff;
    height: 100vh;
    padding: 4em;
    position: absolute;
    overflow-y: hidden;
    top: 0;
    transition: right 0.5s;
    width: 55vw;
  }

  .slide-title {
    font-family: 'NOIRetBLANC', serif;
    font-weight: 100;
    margin: 0;
    margin-top: 2em;
    padding: 0;
    position: relative;
    text-transform: uppercase;
  }

  .title-small {
    display: block;
    font-size: 1.25em;
    font-style: italic;
    position: absolute;
  }

  .title-large {
    display: block;
    color: #FFF22F;
    font-size: 7.5em;
  }

  .slide-out {
    right: -65vw;
  }

  .slide-in {
    right: 0;
  }

  .svg-close {
    cursor: pointer;
    padding: 0;
    width: 4em;
    position: absolute;
    transform: translateY(-10px);
    right: 2em;
    top: 4em;
    z-index: 2;
  }

  .user-icon {
    background-image: url('https://randomuser.me/api/portraits/lego/8.jpg');
    background-size: cover;
    border: 2px solid #FFF22F;
    border-radius: 50%;
    height: 2.5em;
    margin-right: 0.5em;
    position: absolute;
    width: 2.5em;
    right: 6em;
    top: 4em;
  }
</style>
